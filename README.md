

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
