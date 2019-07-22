
pipeline {
    agent any
   
    parameters {
        string(name: 'WORKSPACE', defaultValue: 'development', description:'setting up workspace for terraform')
    }
    environment {
        TF_HOME = tool name: 'Terraform', type: 'org.jenkinsci.plugins.terraform.TerraformInstallation'

        TF_IN_AUTOMATION = "true"
        PATH = "$TF_HOME:$PATH"
        ACCESS_KEY = credentials('ACCESS_KEY')
        SECRET_KEY = credentials('SECRET_KEY')
        
    }
    stages {
            stage('TerraformInit'){
            steps {
                    sh "terraform init --backend-config='access_key=$ACCESS_KEY' ---backend-config='secret_key=$SECRET_KEY' "
                    sh 'terraform --version'
                    sh "echo \$PWD"
                    sh "whoami"
                }
            }
	        stage('TerraformPlan'){
            steps {
                    script {
                        try {
                            sh "terraform workspace new ${params.WORKSPACE}"
                        } catch (err) {
                            sh "terraform workspace select ${params.WORKSPACE}"
                        }
                        sh "terraform plan -var 'access_key=$ACCESS_KEY' -var 'secret_key=$SECRET_KEY' \
                        -out terraform.tfplan;echo \$? > status"
                        stash name: "terraform-plan", includes: "terraform.tfplan"
                    }
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
                        
                           
                         unstash "terraform-plan"
                            sh 'terraform apply terraform.tfplan'
                    }
                }
            }
        }
    }
}
