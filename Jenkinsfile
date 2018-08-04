pipeline {

    agent any

    stages {

        stage('setup') {
            sh "echo Iniciando configuracion..."
        }

        stage('test') {
            sh "echo Ejecutando pruebas unitarias..."            
        }

        stage('integration-test') {
            sh "echo Ejecutando pruebas de integracion..."
        }

        stage('build') {
            sh "echo Ejecutando build..."
        }

        stage('archive') {
            sh "echo 'Archivando artefacto en el repositorio...'"
        }

        stage('deploy') {
            sh "echo 'Desplegando artefacto...'"
        }

        stage('notification') {
            sh "echo 'Notificando resultados...'"
        }
    }

}