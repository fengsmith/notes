常用 maven 命令：
mvn clean
mvn compile
mvn test-compile
mvn test
mvn package
mvn install

mvn deploy:deploy-file -DgroupId=id -DartifactId=id -Dversion=versionNo -Dpackaging=jar -Df
ile=xx.jar -Durl=http://localhost:8081/nexus/content/repositories/snapshots/ -DrepositoryId=Snapshots

url 和 repositoryId 需要在 nexus 私服上去查看
