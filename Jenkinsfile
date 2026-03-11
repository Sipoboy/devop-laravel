node {
  checkout scm

  stage("Build") {
    docker.image('composer:2').inside('-u root') {
      sh 'php -v'
      sh 'composer --version'
      sh 'rm -f composer.lock'
      sh 'composer install'
    }
  }

  stage("Testing") {
    docker.image('ubuntu').inside('-u root') {
      sh 'echo "Ini adalah test"'
    }
  }
}
