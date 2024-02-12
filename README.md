# 🚀 Otto.de high performance Solr

This [Solr](/apache/solr) fork repository contains bugfixes
and optimizations targeted for upstream Solr. It is our working 
repository to file pull requests against the upstream repo. It
furthermore contains bugfixes and improvements that not yet made
it to the upstream repository.

## Improvements and bugfixes

> ✅ checked issues have been successfully merged to upstream\
> ⏳ waiting for approval\
> ✨ new fix, need to filed and pull requested

* ✅ [SOLR-15377](https://issues.apache.org/jira/browse/SOLR-15377) Do not swallow exceptions thrown in replication
* ✅ [SOLR-16489](https://issues.apache.org/jira/browse/SOLR-16489) CaffeineCache puts thread into infinite loop
* ✅ [SOLR-16515](https://issues.apache.org/jira/browse/SOLR-16515) Remove synchronized access to cachedOrdMaps in SlowCompositeReaderWrapper
* ⏳ [SOLR-16497](https://issues.apache.org/jira/browse/SOLR-16497) Allow finer grained locking in SolrCores
* ⏳ [SOLR-10059](https://issues.apache.org/jira/browse/SOLR-10059) In SolrCloud, every fq added via `<lst name="appends">` is computed twice.

