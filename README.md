
stringl=$(if [ -f /eClinicalWorks/prodapps/$tomcatname/webapps/mobiledoa/WEB-INF/lib/ix-ecw-emr-fhir-server-2023-06-26-v-0.0.90.jar ] || [ -f /eClinicalWorks/prodapps/$tomcatname/webapps/mobiledoc/WEB-INF/lib/ix-ecw-emr-fhir-server-uds-2024-06-04.v.6.0.8.jar ]; then echo $tomcatname; fi;)
