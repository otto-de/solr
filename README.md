# 🚀 Otto.de high performance Solr

This [Solr](/apache/solr) fork repository contains bugfixes
and optimizations targeted for upstream Solr. It is our working 
repository to file pull requests against the upstream repo. It
furthermore contains bugfixes and improvements that not yet made
it to the upstream repository.

## 📦 Improvements and bugfixes

> ✅ checked issues have been successfully merged to upstream\
> ⏳ waiting for approval\
> ✨ new fix, needs to filed and pull requested

* ⏳ [SOLR-10059](https://issues.apache.org/jira/browse/SOLR-10059) In SolrCloud, every 
  fq added via `<lst name="appends">` is computed twice. This breaks the collapse filter 
  if configured. Our fix turns this in a different direction, and 
  [sanitizes macros in appended fq parameters](/otto-de/solr/tree/feature/SOLR-10059)
* ⏳ [SOLR-17187](https://issues.apache.org/jira/browse/SOLR-17187) Add the ability to 
  [supply a custom poll interval](/otto-de/solr/tree/feature/replica-custom-poll-interval)
  in the `updateHandler`. This is of interest for TLOG/PULL replica setups with longer commit
  intervals.

#### Pending fixes

* ⏳ [SOLR-16497](https://issues.apache.org/jira/browse/SOLR-16497) Allow finer grained locking in SolrCores

#### New fixes

* ✨ [SOLR-xxxx](https://github.com/otto-de/solr/commits/feature/instrumented-shardhandler) Add an
instrumented `HttpShardHandlerFactory`


#### Merged fixes

* ✅ [SOLR-17185](https://issues.apache.org/jira/browse/SOLR-17185) Open up 
  `ValueAugmenterFactory.ValueAugmenter` for extension
* ✅ [SOLR-15377](https://issues.apache.org/jira/browse/SOLR-15377) Do not swallow exceptions 
  thrown in replication
* ✅ [SOLR-16489](https://issues.apache.org/jira/browse/SOLR-16489) CaffeineCache puts thread 
  into infinite loop
* ✅ [SOLR-16515](https://issues.apache.org/jira/browse/SOLR-16515) Remove synchronized access to 
  cachedOrdMaps in SlowCompositeReaderWrapper

## 👩‍💻 Using this fork repository

To incorporate the bugfixed sources and Java releases in your project,
add the GitHub Maven Package Repository to your Maven or Gradle file.

```xml
<project>
  <repositories>
    <repository>
      <id>otto-de-solr</id>
      <name>Otto.de Solr Fork</name>
      <url>https://maven.pkg.github.com/otto-de/solr</url>
    </repository>
  </repositories>
</project>
```

Docker images are published for both `arm64` and `amd64` architectures:

```bash
docker run -itp 8983:8983 ghcr.io/otto-de/solr:9.5.0
```

> There is no `latest` tag available for the Docker images


## 👩‍💻 Working with this fork

Our goal is to get all improvements merged into upstream. We'll file all our
pull requests based on this repository. Before doing any work, please
familiarize yourself with the [Solr CONTRIBUTING](https://github.com/apache/solr/blob/main/CONTRIBUTING.md) guidelines.


### ✨ Adding / working on a new improvement or fix

> Do not pollute `.gitignore` settings with your local specialities. 
> Use `.git/info/exclude` for local git ignores

1. If not yet present, open an issue in the [Solr Jira Bugtracker](https://issues.apache.org/jira/projects/SOLR/issues/SOLR-16781?filter=allopenissues).
   Use the issue number to label your branch and future commits.
1. Create `feature/*` branch for your feature that forks 
   off the `main` branch
1. Add the [branch-test.yaml](.github/workflows/branch-test.yaml) 
   Github Action Workflow to your new branch
1. After finishing improving Solr, run `./gradlew tidy`
   to properly format your source code
1. Check all formalities via `./gradlew check`
1. Push your new branch

## 🚀 Releasing a new version

Clone the fork repository and prepare 
the `upstream` source git repository.

```bash
git remote -v
git remote add upstream https://github.com/apache/solr.git
git fetch upstream
```

```bash
sdk use java 11.0.21-tem
```

### 🔁 Updating a existing release version

If you want to release a new bugfix version of a already existing Solr release
(say `9.5.0-otto-de.2` over `9.5.0-otto-de.1`), follow these steps.

1. Locate the current release candidate branch. For Solr `9.5.x`
   this would be `candidate/branch_9_5`
1. Sync the Apache Solr release branch `branch_9_5` using the GitHub UI
1. Rebase our release candidate branch onto the Apache Solr release
   branch and replay all Cherry Pick Commits. Force push our 
   release candidate branch to GitHub

```bash
git checkout candidate/branch_9_5
git rebase branch_9_5 --reapply-cherry-picks
git push origin candidate/branch_9_5 --force
```

4. Now cherry pick the new features/issues onto the candidate branch
5. Run `./gradlew check`
6. Push changes to candidate branch

### 🎯 Releasing a new minor version

If you want to release a new bugfix version of a new Solr release
(say `9.6.0-otto-de.1` over `9.5.0-otto-de.5`), follow these steps.

1. __Fork the Solr minor version release branch__, e.g. `branch_9_6`
   into our fork repository

```bash
$ git checkout branch_9_6
$ git push origin branch_9_6
```

2. __Create a bugfix branch__ `candiates/branch_9_6` branching off
   the Solr minor release branch

```bash
$ git checkout -b candidates/branch_9_6
```

3. __Add our test and relase Github Action Workflows__ to your 
   new branch. Run the `branch-test.yaml` GitHub Action manually
   after push to check the branches baseline.

```bash
$ curl -fsLo .github/workflows/branch-test.yaml \
   "https://raw.githubusercontent.com/otto-de/solr/about-this-fork/.github/workflows/branch-test.yaml"
$ curl -fsLo .github/workflows/release.yaml \
   "https://raw.githubusercontent.com/otto-de/solr/about-this-fork/.github/workflows/release.yaml"
$ git add .github/workflows/branch-test.yaml
$ git add .github/workflows/release.yaml
$ git commit -m "Add branch test and release action"
$ git push origin candidates/branch_9_6
```

4. __Cherry pick all fixes from the `features/**` branches__ to our 
   candidate branch.  For each fix, verify that they are still needed!
   Some may have been merged in the meantime!
   After each cherry-pick make sure things integrate 
   well (via `./gradlew check`). 

```
# [SOLR-10059]
$ git cherry-pick a82b500d3c633621b0062698f540c2974a920fbb

# [SOLR-17187]
$ git cherry-pick 533950b0f58c44e86a38980685e267643a4be1a9

# instrumented shard handler
$ git cherry-pick 394fab8611d25f2568a86a11d584ee77af656907

# [SOLR-16497]
$ git cherry-pick 444e8eec26e45e7f5a128d97282cf4ffc47e8898
```

5. __Push your changes to the candidate branch__ and
   trigger the [Branch Test GitHub Action](https://github.com/otto-de/solr/actions/workflows/branch-test.yaml) for your new release candidate
   branch.
6. __Trigger the [Release GitHub Action](https://github.com/otto-de/solr/actions/workflows/release.yaml)__ to build and publish a new release
