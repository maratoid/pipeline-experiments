#!/usr/bin/env groovy
@Library('pipeline')
import net.zonarsystems.pipeline.PipelineHelpers
import net.zonarsystems.pipeline.PipelineBuilders
import groovy.transform.Field
import hudson.EnvVars
import org.jenkinsci.plugins.workflow.cps.EnvActionImpl
import hudson.model.Cause

@Field final String PIPELINE = 'upstream'

def getUpstreamEnv() {
  def upstreamEnv = new EnvVars()
  def upstreamCause = currentBuild.rawBuild.getCause(Cause$UpstreamCause)

  if (upstreamCause) {
    def upstreamJobName = upstreamCause.properties.upstreamProject
    def upstreamBuild = Jenkins.instance
                            .getItemByFullName(upstreamJobName)
                            .getLastBuild()
    upstreamEnv = upstreamBuild.getAction(EnvActionImpl).getEnvironment()
  }

  return upstreamEnv
}

def getParamByName (name) {
  return getUpstreamEnv().get(name)
}

def getDependencies (dependendcyName) {
  def keyset = getUpstreamEnv().keySet()
  def deps = [:]
  for (i = 0; i < keyset.size(); i++) {
    if (keyset[i].startsWith('PARAMETER_'))
    deps << []
  }

  return getUpstreamEnv().DEPENDENCY_NAME
}

podTemplate(label: "jenkins-gke-${PIPELINE}", containers: [
  containerTemplate(name: 'gke', image: 'gcr.io/sds-readiness/jenkins-gke:latest', ttyEnabled: true, command: 'cat', alwaysPullImage: true),
],
volumes: []) {
  container('gke') { 
    checkout scm
    env.HELMPARAM_images_agentImage ='gcr.io/blah/agent:12345'
    env.HELMPARAM_images_masterImage = 'gcr.io/blah/master:12345'
    env.HELMPARAM_nodePorts_nginx = 32000
    env.HELMPARAM_nodePorts_jenkins = 32004
    env.HELMPARAM_services_backend_proxy = 'something something'
    env.HELMPARAM_services_other = 'darkside'
    env.HELMREQ_loadbalancer = '1.4.5'
  }
}
