pipeline {

    agent any

    stages {

        stage('setup') {
            echo 'Iniciando configuracion...'
        }

        stage('test') {
            echo 'Ejecutando pruebas unitarias...'
        }

        stage('integration-test') {
            echo 'Ejecutando pruebas de integracion...'
        }

        stage('build') {
            echo 'Ejecutando build...'
        }

        stage('archive') {
            echo 'Archivando artefacto en el repositorio...'
        }

        stage('deploy') {
            echo 'Desplegando artefacto...'
        }

        stage('notification') {
            echo 'Notificando resultados...'
        }
    }

}