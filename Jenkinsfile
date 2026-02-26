node {
  try {

    stage('Build') {
      sh '''
        set -e
        echo "Building Java project..."
        echo "Listing workspace contents:"
        ls
        cd "Password Protection"
        mkdir -p build
        javac --release 11 -d build src/*.java
        echo "Build successful"
      '''
    }

    stage('Test') {
      sh '''
        set -e
        echo "Running JUnit tests for File-Encrypter..."
        cd "Password Protection"

        echo "Downloading JUnit..."
        rm -f junit-platform-console-standalone.jar
        curl -L -o junit-platform-console-standalone.jar https://repo1.maven.org/maven2/org/junit/platform/junit-platform-console-standalone/1.10.0/junit-platform-console-standalone-1.10.0.jar

        mkdir -p test-build

        if ls test/*.java 1> /dev/null 2>&1; then
          javac -cp junit-platform-console-standalone.jar:build -d test-build test/*.java
          java -jar junit-platform-console-standalone.jar --class-path build:test-build --scan-class-path
          echo "JUnit tests executed successfully"
        else
          echo "No test files found. Skipping tests."
        fi
      '''
    }

    stage('Deploy') {
      sh '''
        set -e
        echo "Deploying (Packaging) File-Encrypter Application..."
        cd "Password Protection"
        jar cf FileEncrypter.jar -C build .
        echo "Deployment successful - Artifact ready"
      '''
    }

    echo "Pipeline executed successfully!"

  } catch (Exception e) {
    echo "Pipeline failed!"
    throw e
  }
}
