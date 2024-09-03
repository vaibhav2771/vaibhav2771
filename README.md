DELIMITER //

CREATE PROCEDURE disable_tomcat3 (
    IN p_newname VARCHAR(255),
    IN p_appid VARCHAR(255), -- Assuming appid is a string containing multiple values
    IN p_podid INT,
    IN p_portno INT,
    IN p_executed_by VARCHAR(255),
    IN p_IRNUMBER VARCHAR(20)
)
BEGIN
    DECLARE p_message VARCHAR(255);
    DECLARE v_limit INT;

    -- Split appid into a list if it's a string
    SET p_appid = REPLACE(p_appid, ',', ','); -- Replace ',' with ', ' to handle multiple commas
    SET @appid_list = CONCAT('(', p_appid, ')');

    -- Get the number of records for the specified podid and appid list
    SELECT COUNT(*) INTO v_limit FROM tomcats WHERE podid = p_podid AND appid IN @appid_list;

    -- Update the data with the dynamic limit (or without limit if needed)
    UPDATE tomcats SET ... WHERE podid = p_podid AND appid IN @appid_list LIMIT v_limit;

    -- ... rest of the procedure ...
END //

DELIMITER ;


DELIMITER //

CREATE PROCEDURE update_ebo_data (
    IN p_eboversion INT,
    IN p_podid INT,
    IN p_practicefolder VARCHAR(255)
)
BEGIN
    DECLARE v_limit INT;

    -- Get the number of records for the specified practice folder
    SELECT COUNT(*) INTO v_limit FROM ebo WHERE practicefolder = p_practicefolder;

    -- Update the data with the dynamic limit
    IF v_limit > 0 THEN
        UPDATE ebo
        SET eboversion = p_eboversion
        WHERE podid IN (p_podid)
        AND eboversion != p_eboversion
        AND practicefolder = p_practicefolder
        LIMIT v_limit;
    ELSE
        SIGNAL SQLSTATE '45000' SET MESSAGE_TEXT = 'No records found for the specified practice folder.';
    END IF;
END //

DELIMITER ;
