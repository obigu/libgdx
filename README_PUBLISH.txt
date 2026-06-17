METHOD 1
========
1. Change libGDX version on gradle.properties and Version.java
2. Make sure  "apply plugin: 'signing'" is commented out (as well as signing configuration block) on publish.gradle.
3. Add allowInsecureProtocol = true to repo definition on publish.gradle
4. Run command gradlew.bat -P RELEASE -PMAVEN_USERNAME=admin -PMAVEN_PASSWORD=XXXXXX -PRELEASE_REPOSITORY_URL=http://192.168.1.40:8081/nexus/content/repositories/thirdparty/ publish
   ((((Make sure to replace password))))


METHOD 2
========
1. Change libGDX version on gradle.properties and Version.java
2. Create a file in root folder called overriderepo.gradle with the following contents:

def repoName = 'nexus'
def repoUrl = 'http://192.168.1.40:8081/nexus/content/repositories/thirdparty/'
allprojects {
    pluginManager.withPlugin( 'maven-publish' ) {
        publishing {
            repositories {
                maven {
                    name = repoName
                    url = repoUrl
                    credentials {
                        username = nexusUsername
                        password = nexusPassword
                    }
                    allowInsecureProtocol true
                }
            }
        }
        tasks.withType( PublishToMavenRepository ) {
            onlyIf {
                repository.name == repoName
            }
        }
    }

    gradle.taskGraph.whenReady {
        tasks.withType(Sign) {
            onlyIf { false }
        }
    }
}


3. Run command

> gradlew -I overriderepo.gradle -PRELEASE -PnexusPassword=XXXXXXX -PnexusUsername=admin publish