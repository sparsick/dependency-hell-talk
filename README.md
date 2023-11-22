# dependency-hell-talk



## Runbook

### Renovate

Demo project: 

´´´´shell
cd demo-project
´´´´

**Preparations:**

1. Delete `renovate.json` in the demo project 
´´´´shell
git reset --hard f732276
git push --force
´´´´
2. Delete `Dependency Dashboard` issue in the demo project
3. Rename the old ` Configure Renovate` PR.
2. Remove the line `prCommitsPerRunLimit : "5"` in `.renovate.json`
3. Create a valid API Token in your GitHub Profile `Developer Setting -> Personal Access token`


**Demo steps:**
1. Configure Renovate Runner, which repository should be updated.
````shell
cat .renovate/config.js
````
2. Run Renovate to initialize the target repository
´´´´shell
docker run --rm -v "$PWD/.renovate/config.js:/usr/src/app/config.js" -e RENOVATE_TOKEN=´<yourgithubapitoken> renovate/renovate

´´´´
3. Merge the initial PR in the target repository
4. Configure `.renovate.json`
´´´´shell
{
  "extends": ["config:recommended"],
  "labels": ["dependencies"]
}
´´´´
Renovate Presets: https://docs.renovatebot.com/presets-config/#configrecommended


5. Configure global Renovate Config: `prCommitsPerRunLimit : "5"`


### OpenRewrite

1. Change to the branch ´openrewrite-demo´ in the demo project.
2. Discover available recipes
´´´´śhell
mvn org.openrewrite.maven:rewrite-maven-plugin:discover \
    -Drewrite.recipeArtifactCoordinates=org.openrewrite.recipe:rewrite-migrate-java:LATEST
´´´´
2. Migrate Java 8 to Java 11
´´´´shell
 mvn org.openrewrite.maven:rewrite-maven-plugin:run \
    -Drewrite.recipeArtifactCoordinates=org.openrewrite.recipe:rewrite-migrate-java:LATEST \
    -Drewrite.activeRecipes=org.openrewrite.java.migrate.Java8toJava11
mvn clean verify
sdk use java 11.0.19-tem
mvn clean verify
´´´´
3. Discover receipes for migrating JUnit4 to JUnit5
´´´śhell
mvn org.openrewrite.maven:rewrite-maven-plugin:discover \
    -Drewrite.recipeArtifactCoordinates=org.openrewrite.recipe:rewrite-spring:LATEST | grep -i junit
´´´
4. Migrate JUnit4 to JUnit 5
´´´´shell
 mvn org.openrewrite.maven:rewrite-maven-plugin:run \
    -Drewrite.recipeArtifactCoordinates=org.openrewrite.recipe:rewrite-spring:LATEST \
    -Drewrite.activeRecipes=org.openrewrite.java.spring.boot2.SpringBoot2JUnit4to5Migration
mvn clean verify
´´´´

