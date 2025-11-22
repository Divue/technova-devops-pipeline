pipeline {

    agent any

    tools {
        maven 'Maven-3.6.0'
        jdk 'jdk1.8.0'
    }

    stages {

        stage('Build') {
            steps {
                checkout scm
                withEnv(["PATH+MAVEN=${tool 'Maven-3.6.0'}/bin"]) {
                    sh "mvn -X clean compile"
                }
            }
        }

        stage('Test') {
            steps {
                echo "Perform Unit Test"
                withEnv(["PATH+MAVEN=${tool 'Maven-3.6.0'}/bin"]) {
                    sh "mvn -X clean test"
                }

                junit '**/target/surefire-reports/TEST-*.xml'

                echo "Perform Integration Test"

                echo "SonarQube Integration"
                sh "mvn clean package sonar:sonar"

                echo "Security Scan Placeholder"
            }
        }

        stage('Package') {
            steps {
                withEnv(["PATH+MAVEN=${tool 'Maven-3.6.0'}/bin"]) {
                    sh "mvn -X clean deploy"
                }
            }
        }

        stage('Provision Infrastructure') {
            steps {
                echo "Provisioning Azure VM for TechNova"
                dir("terraform") {
                    sh '''
                        export PATH=$PATH:/usr/local/bin
                        terraform init
                        terraform plan -out=tfplan
                        terraform apply -auto-approve
                        terraform output -json public_ip_address | jq '.value' > ../ansible/environments/test/hosts
                    '''
                }
            }
        }

        stage('Deploy Application') {
            steps {
                echo "Deploying Application to Azure VM via Ansible"
                sh '''
                    export ANSIBLE_HOST_KEY_CHECKING=False
                    ansible-playbook ansible/playbooks/deploy.yml \
                    -i ansible/environments/test/hosts \
                    -u azureuser
                '''
            }
        }

        stage('Load Test') {
            steps {
                echo "Triggering Load Test Job"
                build job: 'JMeter - Freestyle'
            }
        }

        stage('Confirm Destroy') {
            steps {
                script {
                    input(
                        id: 'confirm',
                        message: 'Destroy Azure VM after testing?',
                        parameters: [
                            [$class: 'BooleanParameterDefinition', defaultValue: false, description: 'Confirm destroy', name: 'confirm']
                        ]
                    )
                }
            }
        }

        stage('Destroy Infrastructure') {
            steps {
                echo "Destroying Azure VM"
                dir("terraform") {
                    sh '''
                        export PATH=$PATH:/usr/local/bin
                        terraform destroy -auto-approve
                    '''
                }
            }
        }
    }
}
