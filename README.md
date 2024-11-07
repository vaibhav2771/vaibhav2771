

To grep Tomcat version from "ps aux | grep tomcat", use:

*Using grep*

```
bash
ps aux | grep tomcat | grep -oP 'tomcat[\w-]+\/\K[\d.]+'
```

*Using sed*

```
bash
ps aux | grep tomcat | sed -rn 's/.*tomcat[^0-9]*([0-9.]+).*/\1/p'
```

*Using awk*

```
bash
ps aux | grep tomcat | awk -F '/' '{print $NF}' | awk -F ' ' '{print $1}'
```

These commands extract the Tomcat version from the process list.

*Explanation*

1. `ps aux`: Lists all processes.
2. `grep tomcat`: Filters Tomcat-related processes.
3. `grep -oP`, `sed`, or `awk`: Extracts the version number.

*Tips*

1. Adjust the pattern to match your Tomcat version format.
2. Use `pgrep` instead of `ps aux | grep` for better performance.
3. Verify the Tomcat version in the output.






ps aux | grep -E 'tomcat.*java' | grep -v grep





DELIMITER ps -ef | grep tomcat | grep -v grep | awk '{print $11}' | grep -E "(tomcat[6789][01]_[A-Za-z0-9]{2,10})"
/

CREATE PROCEDURE update_ccmrtomcats_dynamic (
    IN p_tablename VARCHAR(255),
    IN p_tomcatname VARCHAR(255),
    IN p_ccmrappids TEXT,
    IN p_component VARCHAR(255),
    IN p_subcomponent VARCHAR(255)
)
BEGIN
    DECLARE v_affected_rows INT;
    DECLARE v_error_message VARCHAR(255);
    DECLARE v_count INT;
    DECLARE v_limit INT;

    -- Validate input parameters
    IF p_tablename IS NULL OR p_tomcatname IS NULL OR p_ccmrappids IS NULL OR p_component IS NULL OR p_subcomponent IS NULL THEN
        SET v_error_message = 'All input parameters are required.';
        SIGNAL SQLSTATE '45000' SET MESSAGE_TEXT = v_error_message;
    END IF;

    -- Count the number of matching rows
    SET @count_query = CONCAT('SELECT COUNT(*) INTO @v_count FROM ', p_tablename, ' WHERE tomcatname = ? AND ccmrappid IN (', p_ccmrappids, ')');
    PREPARE count_stmt FROM @count_query;
    EXECUTE count_stmt USING p_tomcatname;
    DEALLOCATE PREPARE count_stmt;

    -- Set LIMIT based on the number of rows fetched
    SET v_limit = @v_count;

    -- Update the table
    SET @update_query = CONCAT('UPDATE ', p_tablename, ' SET COMPONENT = ?, subcomponent = ? WHERE tomcatname = ? AND ccmrappid IN (', p_ccmrappids, ') LIMIT ', v_limit);
    PREPARE update_stmt FROM @update_query;
    EXECUTE update_stmt USING p_component, p_subcomponent, p_tomcatname;
    DEALLOCATE PREPARE update_stmt;

    -- Get the number of affected rows
    SET v_affected_rows = ROW_COUNT();

    -- Log the procedure execution and result
    INSERT INTO storedprocedure_logs (procedure_name, affected_rows, error_message)
    VALUES ('update_ccmrtomcats_dynamic', v_affected_rows, v_error_message);

END //

DELIMITER ;
