pipeline{
   agent any
  stages{
   
      stage("check code"){
        steps{
          echo 'checking code from github'
           checkout scm
}
}


    stage("validating"){
      steps{
        echo 'validating'
        sh 'docker compose config -q'
}
}

    stage("building images"){
      steps{
       echo 'building frontend backend images'
         sh 'docker compose build frontend backend'
}
   }

   
    stage("deploying"){
      steps{
        echo 'deploying containers'
         sh 'docker compose up -d'
}
}

    stage("checking containers"){
       steps{
         echo 'checking containers'
          sh ' docker compose ps'
}
}

       stage("health check"){
          steps{
          echo 'website health check'
           sh 'curl -fsS -o /dev/null http://'13.126.49.177
}
}
}

  post{
    success{
       echo 'pipeline is successfully running'
}

    failure{
        echo 'pipeline failed check logs'
}
}
}
