pipeline {
    agent any
    options {
        skipDefaultCheckout(true)
    }
    stages {
        stage('Code checkout from GitHub') {
            steps {
                script {
                    cleanWs()
                    git credentialsId: 'github-token', url: 'https://github.com/dwieczorek19/abcd-student', branch: 'main'
                    sh 'git clone --mirror https://github.com/dwieczorek19/abcd-student.git'
                }
            }
        }
        // stage('[ZAP] Baseline passive-scan') {
        //     steps {
        //         sh 'mkdir -p results/'
        //         sh '''
        //             docker run --name juice-shop -d --rm \
        //                 -p 3000:3000 \
        //                 bkimminich/juice-shop
        //             sleep 5
        //         '''
        //         sh 'cd zap && chmod 666 passive.yaml'
        //         sh '''
        //             docker run --name zap \
        //                 --add-host=host.docker.internal:host-gateway \
        //                 -v "/var/jenkins_home/workspace/Dominika-DevSecOps/zap:/zap/wrk/:rw" \
        //                 -t ghcr.io/zaproxy/zaproxy:stable bash -c \
        //                 "zap.sh -cmd -addonupdate; zap.sh -cmd -addoninstall communityScripts -addoninstall pscanrulesAlpha -addoninstall pscanrulesBeta -autorun /zap/wrk/passive.yaml"
        //         '''
        //     }
        //     post {
        //         always {
        //             sh '''
        //                 docker cp zap:/zap/wrk/reports/zap_html_report.html ${WORKSPACE}/results/zap_html_report.html
        //                 docker cp zap:/zap/wrk/reports/zap_xml_report.xml ${WORKSPACE}/results/zap_xml_report.xml
        //                 docker stop zap juice-shop
        //                 docker rm zap
        //             '''
        //             defectDojoPublisher(artifact: 'results/zap_xml_report.xml', 
        //             productName: 'Juice Shop', 
        //             scanType: 'ZAP Scan', 
        //             engagementName: 'dominika.wieczorek@xtb.com')
        //         }
        //     }
        // }
        // stage('SCA scan') {
        //     steps {
        //         sh 'osv-scanner scan --lockfile package-lock.json --format json --output results/sca-osv-scanner.json'
        //     }
        //     post {
        //         always {
        //             defectDojoPublisher(artifact: 'results/sca-osv-scanner.json', 
        //                 productName: 'Juice Shop', 
        //                 scanType: 'OSV Scan', 
        //                 engagementName: 'dominika.wieczorek@xtb.com')
        //         }
        //     }
        // }
        stage('Secrets scan') {
            steps {
                sh 'mkdir -p results/'
                sh 'trufflehog git file://abcd-student.git --only-verified --bare --json >> results/trufflehog-scan.json'
            }
            post {
                always {
                    defectDojoPublisher(artifact: 'results/trufflehog-scan.json', 
                        productName: 'Juice Shop', 
                        scanType: 'Trufflehog Scan', 
                        engagementName: 'dominika.wieczorek@xtb.com')
                }
            }
        }
    }
}