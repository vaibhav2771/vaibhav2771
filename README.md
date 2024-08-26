
DELIMITER //

CREATE PROCEDURE update_bulkchefjob_status(
    IN p_eventid INT,
    IN p_podid INT,
    IN p_appid INT
)
BEGIN
    DECLARE exit handler for sqlexception
    BEGIN
        -- Handle SQL exceptions
        ROLLBACK;
        SELECT 'An error occurred: ' || SQLSTATE();
    END;

    UPDATE bulkchefjobs
    SET scheduled = 0, completed = 3
    WHERE eventid = p_eventid AND podid = p_podid AND appid = p_appid
    LIMIT 1;
END //

DELIMITER ;


CALL update_bulkchefjob_status(1205726, 261, 2022);

