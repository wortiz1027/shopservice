pipeline {

    agent any

    stages {

        stage('setup') {
            stpes {
                echo 'Iniciando configuracion...'
            }
        }

        stage('test') {
            steps {
                echo 'Ejecutando pruebas unitarias...'   
            }            
        }

        stage('integration-test') {
            steps {
                echo 'Ejecutando pruebas de integracion...'   
            }            
        }

        stage('build') {
            steps {
                echo 'Ejecutando build...'    
            }            
        }

        stage('archive') {
            steps {
                echo 'Archivando artefacto en el repositorio...'   
            }            
        }

        stage('deploy') {
            steps {
                echo 'Desplegando artefacto...'    
            }            
        }

        stage('notification') {
            steps {
                echo 'Notificando resultados...'    
            }            
        }
    }

}