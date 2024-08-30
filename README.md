

CREATE PROCEDURE update_practoaction (
    IN p_patchid INT,
    IN p_podid INT,
    IN p_executed_by VARCHAR(255),
    IN p_ir_number VARCHAR(20)
)
BEGIN
    DECLARE v_message VARCHAR(100);

    UPDATE practoaction
    SET scheduled = 0, completed = 3
    WHERE patchid = p_patchid AND podid = p_podid;

    IF ROW_COUNT() > 0 THEN
        SET v_message = 'Records updated successfully.';
    ELSE
        SET v_message = 'No records were updated. Please check the conditions.';
    END IF;

    INSERT INTO storedproc_logs (sp_name, executed_by, executed_on, message, IR_Number)
    VALUES ('update_practomaction', p_executed_by, NOW(), v_message, p_ir_number);
END //
