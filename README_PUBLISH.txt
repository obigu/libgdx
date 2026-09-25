
METHOD 1
========
1. Change libGDX version on gradle.properties and Version.java
2. Comment out all org.jreleaser stuff on publish.gradle.
3. Create a file in root folder called overriderepo.gradle with the following contents:

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


4. Run command

> ./gradlew -I overriderepo.gradle -PRELEASE -PnexusPassword=XXXXXXX -PnexusUsername=admin publish