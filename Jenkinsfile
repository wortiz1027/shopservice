#!/usr/bin/env groovy

import groovy.json.JsonOutput

import java.util.Optional

import hudson.tasks.test.AbstractTestResultAction
import hudson.model.Actionable
import hudson.tasks.junit.CaseResult

def author      = ""
def message     = ""
def testSummary = ""
def total       = 0
def failed      = 0
def skipped     = 0
def failedTestsString = "```"

def notification(String type, String status, String color, String text) {
    switch(type) {
        case "slack" : 
                      def slackchannel = "#springboot"
                      slack_notification("",
                                         slackchannel,
                                         [
                                            [
                                                title      : "${jobName}, build #${env.BUILD_NUMBER}",
                                                title_link : "${env.BUILD_URL}",
                                                color      : "${color}",
                                                text       : "${status}\n${author}",
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
                                              color: "${color}",
                                              text: "${failedTestsString}",
                                              "mrkdwn_in": [
                                                            "text"
                                                           ],                                                
                                            ]
                                         ])  

        case "email" : email_notification(text, channel, attachments)
        break
            println "default ${type} - ${status}"
    }
}

def slack_notification(text, channel, attachments) {    
    def slack_user    = "Jenkins"
    def slack_token   = "25DwwRtqn7AVWpeTDGbfmjGc"
    def slack_url     = 'https://soa-developer.slack.com/services/hooks/jenkins-ci?token='
    def slack_Icon    = 'https://wiki.jenkins-ci.org/download/attachments/2916393/logo.png'

    def slack_data  = JsonOutput.toJson([
                                     channel     : channel,
                                     text        : text,                
                                     username    : slack_user,
                                     icon_url    : slack_Icon,
                                     attachments : attachments
                                    ])

    sh "curl -v -X POST --data-urlencode \'payload=${slack_data}\' ${slack_url}${slack_token}"
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
    def mvnHome = tool 'Maven_3_5_4'
    def buildColor = "success"
    def jobName = "${env.JOB_NAME}"
    def url = sh(returnStdout: true, script: 'git config remote.origin.url').trim()
    
    jobName = jobName.getAt(0..(jobName.indexOf('/') - 1))

    stage('setup') { 
        echo 'Iniciando configuracion...' 
        populateGlobalVariables()  
        def buildStatus = currentBuild.result == null ? "Success" : currentBuild.result 

        try {                                        
              checkout scm
              
              notification("slack", buildStatus, buildColor, "Conexion exitosa al repositorio ${url}")
        } catch (err) {
            buildColor = "danger"
            notification("slack", buildStatus, buildColor, "")
        }  // fin try - catch 
    }// fin stage setup

    stage('test') {
        parallel 'unit-test': {
                        try {
                            echo 'Ejecutando pruebas unitarias...'
                        } catch(err) {
                            throw err
                        }                               
                  },
                  'integration-test': {
                        try {
                            echo 'Ejecutando pruebas de integracion...'
                        } catch(err) {
                            throw err   
                        }
                    }
    }        
    

    stage('build') {
                    
    }

    stage('archive') {
                
    }

    stage('deploy') {
                    
    }

    stage('notification') {
                    
    }
  
}