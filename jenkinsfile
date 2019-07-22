
pipeline {
    agent any
   
    parameters {
        string(name: 'WORKSPACE', defaultValue: 'development', description:'setting up workspace for terraform')
    }
    environment {
        TF_HOME = tool name: 'Terraform', type: 'org.jenkinsci.plugins.terraform.TerraformInstallation'

        TF_IN_AUTOMATION = "true"
        PATH = "$TF_HOME:$PATH"
        
    }
    stages {
            stage('TerraformInit'){
            steps {
                    sh "terraform init -input=false"
                    sh 'terraform --version'
                    sh "echo \$PWD"
                    sh "whoami"
                }
            }
         stage('TerraformApply'){
            steps {
                script{
                    def apply = false
                    try {
                        input message: 'Can you please confirm the apply', ok: 'Ready to Apply the Config'
                        apply = true
                    } catch (err) {
                        apply = false
                         currentBuild.result = 'UNSTABLE'
                    }
                    if(apply){
                        
                           
                            sh "terraform apply -var 'access_key=$ACCESS_KEY' -var 'secret_key=$SECRET_KEY' main.tf "
                        
                    }
                }
            }
        }
    }
}
