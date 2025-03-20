pipeline {
  agent any
  stages {
    stage('检出') {
      steps {
        checkout([
          $class: 'GitSCM',
          branches: [[name: env.GIT_BUILD_REF]], 
          userRemoteConfigs: [[url: env.GIT_REPO_URL, credentialsId: env.CREDENTIALS_ID]]
        ])
      }
    }
    stage('生成静态页面') {
      steps {
        script {
          sh 'npm install -g hexo-cli'
          sh 'npm install'
          sh 'hexo g'
        }
      }
    }
    stage('构建') {
      steps {
        echo '构建中...'
        script {
          // 请修改 dockerServer、dockerPath、imageName
          dockerServer = 'wangweicoding-docker.pkg.coding.net'
          dockerPath = '/blog/blog'
          imageName = "${dockerServer}${dockerPath}/blog:${env.GIT_BUILD_REF}"
          def customImage = docker.build(imageName)
 
          // 推送 Docker 镜像到仓库
          docker.withRegistry("https://${dockerServer}", CODING_ARTIFACTS_CREDENTIALS_ID) {
            customImage.push()
          }
        }
      }
    }
  }
}