# 🚀 Otto.de high performance Solr

This [Solr](/apache/solr) fork repository contains bugfixes
and optimizations targeted for upstream Solr. It is our working 
repository to file pull requests against the upstream repo. It
furthermore contains bugfixes and improvements that not yet made
it to the upstream repository.

## 📦 Improvements and bugfixes


* ✅ [SOLR-15377](https://issues.apache.org/jira/browse/SOLR-15377) Do not swallow exceptions thrown in replication
* ✅ [SOLR-16489](https://issues.apache.org/jira/browse/SOLR-16489) CaffeineCache puts thread into infinite loop
* ✅ [SOLR-16515](https://issues.apache.org/jira/browse/SOLR-16515) Remove synchronized access to cachedOrdMaps in SlowCompositeReaderWrapper
* ⏳ [SOLR-10059](https://issues.apache.org/jira/browse/SOLR-10059) In SolrCloud, every fq added via `<lst name="appends">` 
     is computed twice. This breaks the collapse filter if configured.
* ⏳ [SOLR-16497](https://issues.apache.org/jira/browse/SOLR-16497) Allow finer grained locking in SolrCores

> ✅ checked issues have been successfully merged to upstream\
> ⏳ waiting for approval\
> ✨ new fix, needs to filed and pull requested

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

### Releasing a bugfixed version

1. Fork the Solr minor version release branch, e.g. `branch_9_5`
   into our fork repository
1. Create a bugfix branch `candiates/branch_9_5` branching off
   the Solr minor release branch
1. Add the [branch-test.yaml](.github/workflows/branch-test.yaml) 
   Github Action Workflow to your new branch
1. Cherry pick all fixes from the `features/**` branches to our 
   candidate branch and make sure things integrate well (`./gradlew check`)
1. Create and push your changes to the candidate branch. This will
   trigger a last `./gradlew check`
