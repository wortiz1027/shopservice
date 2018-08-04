#!/usr/bin/env groovy

import groovy.json.JsonOutput

import java.util.Optional

import hudson.tasks.test.AbstractTestResultAction
import hudson.model.Actionable
import hudson.tasks.junit.CaseResult

def channel = '#springboot'
def author = ""
def message = ""
def testSummary = ""
def total = 0
def failed = 0
def skipped = 0
def failedTestsString = "```"

def notification(String type, String status) {
    switch(type) {
        case "slack" : slack_notification(text, channel, attachments)
        case "email" : email_notification(text, channel, attachments)
        break
            println "default ${type} - ${status}"
    }
}

def slack_notification(text, channel, attachments) {    
    def slackURL    = 'https://soa-developer.slack.com/services/hooks/jenkins-ci/'
    def jenkinsIcon = 'https://wiki.jenkins-ci.org/download/attachments/2916393/logo.png'

    def payload = JsonOutput.toJson([text: text,
                                     channel  : channel,
                                     username : "Jenkins",
                                     icon_url : jenkinsIcon,
                                     attachments: attachments
                                    ])

    sh "curl -X POST --data-urlencode \'payload=${payload}\' ${slackURL}"
}

def email_notification(text, channel, attachments) {
    
}

def getGitAuthor = {
    def commit = sh(returnStdout: true, script: 'git rev-parse HEAD')
    author = sh(returnStdout: true, script: "git --no-pager show -s --format='%an' ${commit}").trim()
}

def getLastCommitMessage = {
    message = sh(returnStdout: true, script: 'git log -1 --pretty=%B').trim()
}

@NonCPS
def getTestSummary = { ->
    def testAction = currentBuild.rawBuild.getAction(AbstractTestResultAction.class)
    def summary = ""
    if (testAction != null) {
        total   = testAction.getTotalCount()
        failed  = testAction.getFailCount()
        skipped = testAction.getSkipCount() 

        summary = "Passed : ${total - failed - skipped}"
        summary = "${summary}, Failed: ${failed}"
        summary = "${summary}, Skipped: ${skipped}"
    } else {
        summary = "No se encontraron test para ejecutar"
    }

    return summary
}

@NonCPS
def getFailedTests = { ->
    def testResultAction = currentBuild.rawBuild.getAction(AbstractTestResultAction.class)    

    if (testResultAction != null) {
        def failedTests = testResultAction.getFailedTests()

        if (failedTests.size() > 9) {
            failedTests = failedTests.subList(0, 8)
        }

        for(CaseResult cr : failedTests) {
            failedTestsString = failedTestsString + "${cr.getFullDisplayName()}:\n${cr.getErrorDetails()}\n\n"
        }
        failedTestsString = failedTestsString + "```"
    }
    return failedTestsString
}

def populateGlobalVariables = {
    getLastCommitMessage()
    getGitAuthor()
    testSummary = getTestSummary()
}

node {

    /*agent any

    tools {
        jdk 'Jdk_8_181'
        maven 'Maven_3_5_4'
    }*/

        def buildColor = "success"
        def jobName = "${env.JOB_NAME}"

        jobName = jobName.getAt(0..(jobName.indexOf('/') - 1))

        stage("Post to Slack") {
                notifySlack("Success!", channel, [])
            }

        stage('setup') { 
            echo 'Iniciando configuracion...' 
            populateGlobalVariables()  
            def buildStatus = currentBuild.result == null ? "Success" : currentBuild.result 

            try {
                                        
                        slack_notification("",
                                    channel,
                                    [
                                    [
                                        title: "${jobName}, build #${env.BUILD_NUMBER}",
                                        title_link: "${env.BUILD_URL}",
                                        color: "${buildColor}",
                                        text: "${buildStatus}\n${author}",
                                        "mrkdwn_in": [
                                        "fields"
                                        ],
                                        fields: [
                                        [
                                            title: "Branch",
                                            value: "${env.GIT_BRANCH}",
                                            short: true
                                        ],
                                        [
                                            title: "Test Results",
                                            value: "${testSummary}",
                                            short: true
                                        ],
                                        [
                                            title: "Last Commit",
                                            value: "${message}",
                                            short: false
                                        ]
                                        ]
                                    ],
                                    [
                                        title: "Failed Tests",
                                        color: "${buildColor}",
                                        text: "${failedTestsString}",
                                        "mrkdwn_in": [
                                        "text"
                                        ],
                                        
                                    ]
                                    ])
            } catch (err) {
                        buildColor = "danger"
                        slack_notification("",
                                    channel,
                                    [
                                        [
                                            title: "${jobName}, build #${env.BUILD_NUMBER}",
                                            title_link: "${env.BUILD_URL}",
                                            color: "${buildColor}",
                                            text: "${buildStatus}\n${author}",
                                            "mrkdwn_in": [
                                            "fields"
                                            ],
                                            fields: [
                                            [
                                                title: "Branch",
                                                value: "${env.GIT_BRANCH}",
                                                short: true
                                            ],
                                            [
                                                title: "Test Results",
                                                value: "${testSummary}",
                                                short: true
                                            ],
                                            [
                                                title: "Last Commit",
                                                value: "${message}",
                                                short: false
                                            ]
                                            ]
                                        ],
                                        [
                                            title: "Failed Tests",
                                            color: "${buildColor}",
                                            text: "${failedTestsString}",
                                            "mrkdwn_in": [
                                            "text"
                                            ],
                                            
                                        ]
                                        ])
            }  // fin try - catch 
        }// fin stage setup
    
        stage('test') {
           /* steps {
                parallel{
                    stage('unit-test') {
                            try {
                                echo 'Ejecutando pruebas unitarias...'
                            } catch(err) {
                                throw err
                            }                               
                     }

                     stage('integration-test') {
                            try {
                                echo 'Ejecutando pruebas de integracion...'
                            } catch(err) {
                                throw err   
                            }
                     }
                }
            }    */        
        }

        stage('build') {
            /*steps {
                echo 'Ejecutando build...'    
            } */           
        }

        stage('archive') {
            /*steps {
                echo 'Archivando artefacto en el repositorio...'   
            }  */          
        }

        stage('deploy') {
            /*steps {
                echo 'Desplegando artefacto...'    
            } */           
        }

        stage('notification') {
           /* steps {
                echo 'Notificando resultados...'    
            }*/            
        }
  

    /*post {
        always {
            notification("slack", currentBuild.currentResult)
            cleanWs()
        }
    }*/
}