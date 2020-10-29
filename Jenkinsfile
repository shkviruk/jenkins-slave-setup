pipeline {
    agent {
        docker {
            image 'ansible/ansible:default'
            label 'slave-builder'
            args '-u 0:0 -v /home/jenkins/.ssh:/root/.ssh'
        } //docker
    } //agent
    stages {
        stage("Set up") {
            steps {
                sh """
		    apt update
                    apt install ansible --yes
                    pip install ansible
                """
            } //steps
        } //stage
         stage("Build") {
            steps {
                sh """
                    ansible-playbook -i inventory jenkins-slave-setup.yml
                """
            } //steps
        } //stage
        stage("Test") {
            steps {
                sh """
                    ansible jenkins-slaves -m shell -a "ls /home/jenkins/jenkins_slave" -i inventory
                    ansible jenkins-slaves -m shell -a "id jenkins" -i inventory
                    ansible jenkins-slaves -m shell -a "cat /home/jenkins/.ssh/authorized_keys" -i inventory
                """
            } //steps
        } //stage
        stage("Deploy") {
            steps {
                sh """
                    # One method
                    zip "jenkins-slave-setup.zip" .
                    scp jenkins-slave-setup.zip root@webserver:/var/www/html/my-repository
                """
            }
        }
    } //stages
}
