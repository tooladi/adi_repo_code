//Pre-Requisite: Maven tool to be installed!!
node{

        stage ('OWASP Dependency-Check Vulnerabilities') {
           
                deleteDir()
              //  owasp_dc()
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[credentialsId: 'Github', url: 'https://github.com/sneha0302/java-reachability-playground.git']]])
                bat 'mvn compile'
               
                dependencyCheck additionalArguments: ''' 
                   -o "./" 
                    -s "./"
                   -f "ALL" 
                    --prettyPrint''', odcInstallation: 'OWASP-DC'

                dependencyCheckPublisher pattern: 'dependency-check-report.xml'
               
        
        
    }
}

def owasp_dc(){
    
     ws("C:\\Users\\sneha.sathyan\\Downloads\\owasp_dc"){
                   
                    bat "mkdir project"
                    dir("project"){
                        checkout([$class: 'GitSCM', branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[credentialsId: 'Github', url: 'https://github.com/sneha0302/java-reachability-playground.git']]])
                        bat 'mvn compile'
                    }
                    bat "git clone https://github.com/jeremylong/DependencyCheck.git"
                    dir("DependencyCheck"){
                        bat "mvn -s settings.xml install"
                        bat "C:\\Users\\sneha.sathyan\\Downloads\\owasp_dc\\DependencyCheck\\cli\\target\\release\\bin\\dependency-check.bat -h"
                        bat "C:\\Users\\sneha.sathyan\\Downloads\\owasp_dc\\DependencyCheck\\cli\\target\\release\\bin\\dependency-check.bat --out 'report.xml' --scan C:\\Users\\sneha.sathyan\\Downloads\\owasp_dc\\project"
                  }
                  //  bat '${WORKSPACE}+\\DependencyCheck\\bin\\dependency-check.bat --project "mvnwebapp-1" --scan "//*.war" --out "report.html"'
                }
    
}
