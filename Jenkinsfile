properties([
    parameters([
        string (name:'branchName', defaultValue: 'master', description: 'Branch to get the tests from')
    ])
])

def isFailed = false
def branch = params.branchName

currentBuild.description = "Branch: $branch"

node('master') {
    stage('Checkout')
    {
      git branch: branch, url: 'https://github.com/Chacka/AtataCourse.git'
    }
    stage('Restore nuget')
    {
        bat '"C:/Users/Oleksandr Bazurin/Downloads/nuget.exe" restore src/PhpTravels.UITests.sln'
    }
    stage('Build solution')
    {
        bat '"C:/Program Files (x86)/Microsoft Visual Studio/2017/Community/MSBuild/15.0/Bin/MSBuild.exe" src/PhpTravels.UITests.sln'
    }
   
  
}

 catchError 
    {
        isFailed = true
        stage('Run Tests')
        {
            parallel 
            FirstTest: {
                node('master')
                {
                bat '"C:/Program Files (x86)/NUnit.org/nunit-console/nunit3-console.exe" src/PhpTravels.UITests/bin/Debug/PhpTravels.UITests.dll --where cat==First'
                }
            }, 
            SecondTest: {
                node('slave')
                {
                bat '"C:/Program Files (x86)/NUnit.org/nunit-console/nunit3-console.exe" src/PhpTravels.UITests/bin/Debug/PhpTravels.UITests.dll --where cat==Second'
                }
            }
            
        }
        isFailed = false
    }
node('master')
{
  stage("Reporting")
    {
        if(isFailed)
        {
            slackSend color: 'danger', message: 'Test failed'
        }
        else
        {
            slackSend color: 'good', message: 'Test Ok'
        }
    }
}