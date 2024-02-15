# Releasing stuff

This describes building a local release. You should not need to go
though these manual steps, it should be done by GitHub Actions.

## Versioning

We follow [semantic versioning](https://semver.org/) for release versions
(as upstream Solr does). We add the prefix `otto-de` to our releases followed
by an internal release increment.

```bash
export VERSION=9.5.0
export VERSION_SUFFIX=otto-de.1
export VERSION_FULL="${VERSION}-${VERSION_SUFFIX}"
```

## Build Release JARs

```bash
./gradlew assembleRelease -Dversion.release=${VERSION_FULL}
```

### Upload release JARs to GitHub Package Repository

Prepare Maven deployment configuration. The `settings.xml` uses the
environment variables `GITHUB_ACTOR` and `GITHUB_TOKEN` to configure
access to the GitHub Package Repository (as the corresponding GitHub 
Action does)

```bash
cp settings.xml ~/.m2/settings.xml
export GITHUB_ACTOR=tboeghk
export GITHUB_TOKEN=...
```

> 👉 Find your `GITHUB_ACTOR` and `GITHUB_TOKEN` in your `~/.gradle/gradle.properties`

Upload the release JARs

```bash
export MAVEN_ARTIFACTS=$(pwd)/solr/distribution/build/release/maven/org/apache/solr

for artifact_id in `ls -1 ${MAVEN_ARTIFACTS}`; do
    echo "Uploading ${artifact_id} ..."
    export MAVEN_RELEASE_JARS="${MAVEN_ARTIFACTS}/${artifact_id}/${VERSION_FULL}"
    mvn org.apache.maven.plugins:maven-deploy-plugin:3.1.1:deploy-file \
        -Durl=https://maven.pkg.github.com/otto-de/solr \
        -DrepositoryId=github \
        -Dfile=${MAVEN_RELEASE_JARS}/${artifact_id}-${VERSION_FULL}.jar \
        -DpomFile=${MAVEN_RELEASE_JARS}/${artifact_id}-${VERSION_FULL}.pom \
        -Dfiles=${MAVEN_RELEASE_JARS}/${artifact_id}-${VERSION_FULL}-sources.jar,${MAVEN_RELEASE_JARS}/${artifact_id}-${VERSION_FULL}-javadoc.jar \cat
        -Dclassifiers=sources,javadoc \
        -Dtypes=jar,jar
done
```

## Build a Docker image

First, build a local Docker image to check the overall set up. The Gradle build
prepares the custom `Dockerfile` and _Docker Context_ for us.

```bash
export SOLR_DOCKER_IMAGE_REPO=otto-de/solr
export SOLR_DOCKER_IMAGE_TAG=${VERSION}
export SOLR_DOCKER_BASE_IMAGE=eclipse-temurin:21-jre-jammy
export SOLR_DOCKER_DIST=full

./gradlew solr:docker:docker -Dversion.release=${VERSION_FULL} -Dmanifest.username=${GITHUB_ACTOR}
```

Use the prepared Docker Context to build a multi-arch image using Docker `buildx`:

```bash
mkdir -p solr/packaging/build/distributions/context
tar xzvf solr/packaging/build/distributions/solr-${VERSION_FULL}.tgz \
    -C solr/packaging/build/distributions/context
docker buildx build \
    --platform linux/arm64/v8,linux/amd64 \
    -f solr/packaging/build/distributions/context/solr-${VERSION_FULL}/docker/Dockerfile \
    --build-arg BASE_IMAGE=${SOLR_DOCKER_BASE_IMAGE} \
    solr/packaging/build/distributions/context/
```
