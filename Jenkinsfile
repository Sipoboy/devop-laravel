node {

    stage('Checkout') {
        checkout scm
    }

stage('Build') {
    docker.image('php:8.4-cli').inside('--entrypoint="" -u root') {
        sh '''
        apt-get update
        apt-get install -y git unzip curl libzip-dev

        docker-php-ext-install zip

        curl -sS https://getcomposer.org/installer | php
        mv composer.phar /usr/local/bin/composer

        git config --global --add safe.directory /var/jenkins_home/workspace/laravel-dev

        php -v
        composer --version

        composer install --ignore-platform-req=ext-gd
        '''
    }
}

    stage('Testing') {
        docker.image('ubuntu:22.04').inside('--entrypoint="" -u root') {
            sh '''
            echo "Ini adalah test"
            '''
        }
    }

    stage('Deploy') {
        withEnv(["PROD_HOST=172.25.46.154"]) {
            docker.image('agung3wi/alpine-rsync:1.1').inside('--entrypoint="" -u root') {
                sshagent(credentials: ['ssh-prod']) {
                    sh '''
                    mkdir -p ~/.ssh
                    ssh-keyscan -H $PROD_HOST >> ~/.ssh/known_hosts

                    ssh alexr@$PROD_HOST "rm -f /home/alexr/prod.kelasdevops.xyz/bootstrap/cache/packages.php /home/alexr/prod.kelasdevops.xyz/bootstrap/cache/services.php"

                    rsync -rav --delete ./ \
                        alexr@$PROD_HOST:/home/alexr/prod.kelasdevops.xyz/ \
                        --exclude='public/build' \
                        --exclude='node_modules' \
                        --exclude='vendor' \
                        --exclude='storage' \
                        --exclude='.git' \
                        --exclude='.env'
                    '''
                }
            }
        }
    }

}
