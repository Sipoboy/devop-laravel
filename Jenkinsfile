node {
  checkout scm

  stage("Build") {
    docker.image('php:8.2-cli').inside('-u root') {
      sh 'apt-get update'
      sh 'apt-get install -y git curl unzip libzip-dev libpng-dev'
      sh 'docker-php-ext-install gd zip'
      sh 'curl -sS https://getcomposer.org/installer | php'
      sh 'mv composer.phar /usr/local/bin/composer'
      sh 'git config --global --add safe.directory /var/jenkins_home/workspace/laravel-dev'
      sh 'php -v'
      sh 'composer --version'
      sh 'composer install'
    }
  }

  stage("Testing") {
    docker.image("ubuntu").inside("-u root") {
      sh 'echo "Ini adalah test"'
    }
  }

  stage("Deploy Prod") {
    withEnv(["PROD_HOST=172.25.46.154"]) {
      docker.image('agung3wi/alpine-rsync:1.1').inside('-u root') {
        sshagent(credentials: ['ssh-prod']) {
          sh 'mkdir -p ~/.ssh'
          sh 'ssh-keyscan -H "$PROD_HOST" >> ~/.ssh/known_hosts'
          sh 'rsync -rav --delete ./ alexr@$PROD_HOST:/home/alexr/prod.kelasdevops.xyz/ --exclude=.env --exclude=storage --exclude=.git'
        }
      }
    }
  }
}
