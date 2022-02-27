#!/usr/bin/env groovy

node {
    stage('checkout') {
        checkout scm
    }

    stage('check java') {
        sh "java -version"
    }

    stage('clean') {
        sh "chmod +x mvnw"
        sh "mvn -ntp clean -P-webapp"
    }
    stage('nohttp') {
        sh "mvn -ntp checkstyle:check"
    }

    stage('install tools') {
        sh "mvn -ntp com.github.eirslett:frontend-maven-plugin:install-node-and-npm@install-node-and-npm"
    }

    stage('npm install') {
        sh "mvn -ntp com.github.eirslett:frontend-maven-plugin:npm"
    }
    stage('backend tests') {
        try {
            sh "mvn -ntp verify -P-webapp"
        } catch(err) {
            throw err
        } finally {
            junit '**/target/surefire-reports/TEST-*.xml,**/target/failsafe-reports/TEST-*.xml'
        }
    }

    // stage('frontend tests') {
    //     try {
    //         sh "./mvnw -ntp com.github.eirslett:frontend-maven-plugin:npm -Dfrontend.npm.arguments='run test'"
    //     } catch(err) {
    //         throw err
    //     } finally {
    //         junit '**/target/test-results/TESTS-results-jest.xml'
    //     }
    // }

    stage('packaging') {
        sh "mvn -ntp verify -P-webapp -Pprod -DskipTests"
        archiveArtifacts artifacts: '**/target/*.jar', fingerprint: true
    }
}
