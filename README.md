
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



DELIMITER //

CREATE PROCEDURE update_practo_action (
    IN p_patchid INT,
    IN p_podid INT,
    IN p_limit INT
)
BEGIN
    DECLARE v_total_rows INT;

    -- Validate input parameters
    IF p_limit <= 0 THEN
        SIGNAL SQLSTATE '45000' SET MESSAGE_TEXT = 'Limit must be greater than 0';
    END IF;

    -- Calculate the total number of rows that match the criteria
    SELECT COUNT(*) INTO v_total_rows
    FROM practoaction
    WHERE patchid = p_patchid AND podid = p_podid;

    -- Check if any rows match the criteria
    IF v_total_rows = 0 THEN
        SIGNAL SQLSTATE '45000' SET MESSAGE_TEXT = 'No matching records found';
    END IF;

    -- Update the records
    UPDATE practoaction
    SET scheduled = 0, completed = 3
    WHERE patchid = p_patchid AND podid = p_podid
    LIMIT p_limit;
END //

DELIMITER ;

