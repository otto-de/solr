# 🚀 Otto.de high performance Solr
![OSS Lifecycle](https://img.shields.io/osslifecycle?file_url=https%3A%2F%2Fraw.githubusercontent.com%2Fotto-de%2Fsolr%2Fabout-this-fork%2FOSSMETADATA)

This [Solr](/apache/solr) fork repository contains bugfixes
and optimizations targeted for upstream Solr. It is our working 
repository to file pull requests against the upstream repo. It
furthermore contains bugfixes and improvements that not yet made
it to the upstream repository.

## 📦 Improvements and bugfixes

These are the improvements and bugfixes in this release. Some are already
filed as Solr issues, some are pending issues and others have not yet
been submitted.

> [!NOTE]
> ✅ checked issues have been successfully merged to upstream\
> ⏳ waiting for approval\
> ✨ new fix, needs to filed and pull requested\
> ❌ discarded\

* ⏳ [SOLR-10059](https://issues.apache.org/jira/browse/SOLR-10059) In SolrCloud, every 
  fq added via `<lst name="appends">` is computed twice. This breaks the collapse filter 
  if configured. Our fix turns this in a different direction, and 
  [sanitizes macros in appended fq parameters](/otto-de/solr/tree/feature/10/SOLR-10059)
* ⏳ [SOLR-17334](https://issues.apache.org/jira/browse/SOLR-17334) Minor bugs in Solr 
  dedicated coordinator mode. Fix access to the root resource and allow coordinator
  requests outside of the `/select` handler. There are [two](https://github.com/apache/solr/pull/2527) [PRs](https://github.com/apache/solr/pull/2672) that we need to follow
  to get this issue resolved.
* ⏳ [SOLR-16497](https://issues.apache.org/jira/browse/SOLR-16497) Allow finer grained locking in SolrCores ([PR](https://github.com/apache/solr/pull/1155))


#### Merged fixes

* ✅ [SOLR-17686](https://issues.apache.org/jira/browse/SOLR-17686) Our approach to 
  [Re-enable custom stages](https://github.com/otto-de/solr/commits/features/custom-stages)
* ✅ [SOLR-17187](https://issues.apache.org/jira/browse/SOLR-17187) Add the ability to 
  supply a custom poll interval in the `updateHandler`. This is of interest for TLOG/PULL 
  replica setups with longer commit intervals.
* ✅ [SOLR-17337](https://github.com/apache/solr/pull/2526) Show proper 
  distributed stage id
* ✅ [SOLR-17185](https://issues.apache.org/jira/browse/SOLR-17185) Open up 
  `ValueAugmenterFactory.ValueAugmenter` for extension
* ✅ [SOLR-15377](https://issues.apache.org/jira/browse/SOLR-15377) Do not swallow exceptions 
  thrown in replication
* ✅ [SOLR-16489](https://issues.apache.org/jira/browse/SOLR-16489) CaffeineCache puts thread 
  into infinite loop
* ✅ [SOLR-16515](https://issues.apache.org/jira/browse/SOLR-16515) Remove synchronized access to cachedOrdMaps in SlowCompositeReaderWrapper
* ❌ [SOLR-xxxx](https://github.com/otto-de/solr/commits/feature/instrumented-shardhandler)
 Add an instrumented `HttpShardHandlerFactory`

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
docker run -itp 8983:8983 ghcr.io/otto-de/solr:10.0.0
```

> [!NOTE]
> There is no `latest` tag available for the Docker images


## 👩‍💻 Working with this fork

Our goal is to get all improvements merged into upstream. We'll file all our
pull requests based on this repository. Before doing any work, please
familiarize yourself with the [Solr CONTRIBUTING](https://github.com/apache/solr/blob/main/CONTRIBUTING.md) guidelines.


### ✨ Adding / working on a new improvement or fix

> [!NOTE]
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
the `upstream` source git repository:

```bash
git remote -v
git remote add upstream https://github.com/apache/solr.git
git fetch upstream
```

Configure a recent and approriate JDK in the terminal ...

| Solr 9                     | Solr 10                    |
| -------------------------- | -------------------------- |
| `sdk use java 11.0.30-tem` | `sdk use java 21.0.10-tem` |

... and you IDE (example for VSCode and Solr 10):

```json
"java.jdt.ls.java.home": "/Users/torsten.koester/.sdkman/candidates/java/21.0.10-tem",
"java.import.gradle.java.home": "/Users/torsten.koester/.sdkman/candidates/java/21.0.10-tem"
```


### 🔁 Releasing a new patch level version

If you want to release a new bugfix version of a already existing Solr release
(say `9.6.0-otto-de.2` over `9.6.0-otto-de.1`), follow these steps.

1. Locate the current release candidate branch. For Solr `9.6.x`
   this would be `candidates/branch_9_6`
1. Sync the Apache Solr release branch `branch_9_6` using the GitHub UI
1. Rebase our release candidate branch onto the Apache Solr release
   branch and replay all Cherry Pick Commits. Force push our 
   release candidate branch to GitHub

```bash
git checkout candidates/branch_9_6
git rebase branch_9_6 --reapply-cherry-picks
git push origin candidates/branch_9_6 --force
```

4. Now cherry pick the new features/issues onto the candidate branch
5. Run `./gradlew check`
6. Push changes to candidate branch

### 🎯 Releasing a new major/minor version

If you want to release a new major/minor/bugfix version of a new
Solr release (say `9.8.0-otto-de.1` over `9.5.0-otto-de.5`), follow 
these steps.

1. __Fork the Solr minor version release branch__, e.g. `branch_10_0`
   into our fork repository

```bash
$ git fetch upstream
$ git checkout branch_10_0
$ git push origin branch_10_0
```

2. __Create a bugfix branch__ `candiates/branch_10_0` branching off
   the Solr minor release branch

```bash
$ git checkout -b candidates/branch_10_0
```

3. __Add our test and release Github Action Workflows__ to your 
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
$ git push origin candidates/branch_10_0
```

4. __Cherry pick all fixes from the `features/10/**` branches__ to our 
   candidate branch.  For each fix, verify that they are still needed!
   Some may have been merged in the meantime!
   After each cherry-pick make sure things integrate 
   well (via `./gradlew check`). 

```bash
# [SOLR-10059]
$ git cherry-pick 52d2cbe && ./gradlew clean compileJava compileTestJava

# [SOLR-17334] Allow coordinator requests outside of /select
$ git cherry-pick 0b02bc7 && ./gradlew clean compileJava compileTestJava

# [SOLR-16497] Finer grained locking
$ git cherry-pick e24c741 && ./gradlew clean compileJava compileTestJava

# done
$ git push origin candidates/branch_10_0
```

Every push triggers the [Branch Test GitHub Action](https://github.com/otto-de/solr/actions/workflows/branch-test.yaml)
for your new release candidate branch. As soon as the action is green, ...

5. __trigger the [Release GitHub Action](https://github.com/otto-de/solr/actions/workflows/release.yaml)__ 
   to build and publish a new release
