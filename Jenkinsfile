pipeline {
    agent any
    environment {
        //定义环境变量，就是Node.js安装路径，在容器里面
        NODE_HOME = '/home/node-v22.0.0-linux-x64/bin/node'
        PATH = "${NODE_HOME}/bin:${env.PATH}"
    }
    stages {
        stage('拉取代码') {
            steps {
                //拉取git仓库代码
                git branch: 'main', url: 'https://github.com/zxyf118/follow-admin.git'
            }
        }
        stage('安装依赖') {
            steps {
                sh 'npm install' 
            }
        }
        stage('打包构建') {
            steps {
                sh 'npm run build:prod' 
            }
        }
        stage('发布部署') {
            steps {
                //通过SSH、FTP等方式，将dist目录上传到服务器
                sh '''
                scp -r dist/* stock:/data/stock-follow/test-stock-follow-admin/
                '''
            }
        }
        stage('telegram') {
            steps {
                script {
                    def telegramToken = '7510361258:AAFVjVB9pO89HOnK_IvCiETXKv1nUgjBoYg'
                    def chatId = '-4741015981'
                    def message = "Jenkins通知: ${env.JOB_NAME} 构建${currentBuild.currentResult}"
                sh """
                    curl -s -X POST \
                    https://api.telegram.org/bot${telegramToken}/sendMessage \
                    -d chat_id=${chatId} \
                    -d text="${message}"
                """
                }
            }
         }
    }
    post {
        success {
            echo '构建并部署成功！'
        }
        failure {
            echo '构建失败，请检查日志。'
        }
    }
}
