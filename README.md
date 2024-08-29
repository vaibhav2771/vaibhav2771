
stringl=$(if [ -f /eClinicalWorks/prodapps/$tomcatname/webapps/mobiledoa/WEB-INF/lib/ix-ecw-emr-fhir-server-2023-06-26-v-0.0.90.jar ] || [ -f /eClinicalWorks/prodapps/$tomcatname/webapps/mobiledoc/WEB-INF/lib/ix-ecw-emr-fhir-server-uds-2024-06-04.v.6.0.8.jar ]; then echo $tomcatname; fi;)
CREATE PROCEDURE insert_command_if_not_exists (IN p_disp_name VARCHAR(255), IN p_command VARCHAR(255))
BEGIN
    DECLARE v_exists INT;

    -- Check if the command already exists
    SELECT COUNT(*) INTO v_exists FROM command WHERE disp_name = p_disp_name AND command = p_command;

    IF v_exists = 0 THEN
        -- Insert the command if it doesn't exist
        INSERT INTO command (disp_name, command, delflag) VALUES (p_disp_name, p_command, 0);
    END IF;
END;
