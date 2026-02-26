pipeline {
agent any
stages {
stage('Build') {
steps {
sh '''
echo "Building Java project..."
echo "Listing workspace contents:"
ls
cd "Password Protection"
mkdir -p build
javac -d build src/*.java
echo "Build successful"
'''
}
}
stage('Test') {
  steps {
    sh '''
      set -e
      echo "Running JUnit tests for File-Encrypter..."
      cd "Password Protection"

      # Always re-download JUnit to avoid corrupted cache
      echo "Downloading JUnit..."
      rm -f junit-platform-console-standalone.jar
      curl -L -o junit-platform-console-standalone.jar https://repo1.maven.org/maven2/org/junit/platform/junit-platform-console-standalone/1.10.0/junit-platform-console-standalone-1.10.0.jar

      # Verify jar looks valid
      jar tf junit-platform-console-standalone.jar > /dev/null

      # Compile tests only if they exist
      mkdir -p test-build
      if ls test/*.java 1> /dev/null 2>&1; then
        javac -cp junit-platform-console-standalone.jar:build -d test-build test/*.java
        java -jar junit-platform-console-standalone.jar --class-path build:test-build --scan-class-path
      else
        echo "No test files found in test/. Skipping tests."
      fi
    '''
  }
}
stage('Deploy') {
steps {
sh '''
echo "Deploying (Packaging) File-Encrypter Application..."
cd "Password Protection"
# Create executable artifact (JAR)
jar cf FileEncrypter.jar -C build .
echo "Deployment successful - Artifact ready"
'''
}
}
}
post {
success {
echo "Pipeline executed successfully!"
}
failure {
echo "Pipeline failed!"
}
}
}
