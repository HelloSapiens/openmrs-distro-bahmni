FROM openmrs/openmrs-distro-platform:2.1.6-SNAPSHOT

ENV ATOMFEED_CLIENT_VERSION=1.10.1
ENV LIQUIBASE_VERSION=2.0.5

# Creating Config Directories
RUN mkdir -p /usr/local/tomcat/.OpenMRS/modules/
RUN mkdir -p /tmp/artifacts/default_config/
RUN mkdir -p /tmp/artifacts/distro/ 
RUN mkdir -p /etc/bahmni-lab-connect/

# Downloading atomfeed client and liquibase core
RUN curl -L -o /tmp/artifacts/atomfeed-client-${ATOMFEED_CLIENT_VERSION}.jar "https://oss.sonatype.org/content/repositories/releases/org/ict4h/atomfeed-client/${ATOMFEED_CLIENT_VERSION}/atomfeed-client-${ATOMFEED_CLIENT_VERSION}.jar"
RUN curl -L -o /tmp/artifacts/liquibase-core-${LIQUIBASE_VERSION}.jar "https://oss.sonatype.org/content/repositories/releases/org/liquibase/liquibase-core/${LIQUIBASE_VERSION}/liquibase-core-${LIQUIBASE_VERSION}.jar"

#unzip default_config
ADD package/resources/default_config.zip /tmp/artifacts/
RUN unzip -d /tmp/artifacts/default_config/ /tmp/artifacts/default_config.zip 

COPY distro/target/distro/*.omod /usr/local/tomcat/.OpenMRS/modules/
COPY package/docker/openmrs/templates/bahmnicore.properties.template /etc/bahmni-emr/templates/
RUN cp /tmp/artifacts/atomfeed-client-*.jar /etc/bahmni-lab-connect/atomfeed-client.jar
RUN cp /tmp/artifacts/liquibase-core-*.jar /etc/bahmni-lab-connect/liquibase-core.jar
RUN cp -r /tmp/artifacts/default_config/. /etc/bahmni_config/

# Creating Upload Directories
RUN mkdir -p /home/bahmni/patient_images
RUN mkdir -p /home/bahmni/document_images
COPY package/resources/blank-user.png /etc/bahmni/

# Used by envsubst command for replacing environment values at runtime
RUN apt-get update && apt-get install gettext-base

COPY package/docker/openmrs/bahmni_startup.sh /usr/local/tomcat/
RUN chmod +x /usr/local/tomcat/bahmni_startup.sh

# Removing FHIR OMOD that comes bundled with OMRS base image
RUN unzip -o /usr/local/tomcat/webapps/openmrs.war -d /usr/local/tomcat/webapps/openmrs/
RUN rm /usr/local/tomcat/webapps/openmrs/WEB-INF/bundledModules/fhir-*.omod
RUN rm -rf /tmp/artifacts 
CMD ["./bahmni_startup.sh"]
