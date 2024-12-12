
Let's break down Approach 1 and Approach 2:

*Approach 1: Using a unique identifier*

*Syntax:*
```
bash
sed '/<tag>.*unique_identifier/s/old_content/new_content/' document.txt
```
*Explanation:*

1. `/<tag>/`: Searches for the `<tag>` pattern.
2. `.*`: Matches any characters (including none) until the next pattern.
3. `unique_identifier`: A unique string or pattern that identifies the specific line you want to replace.
4. `s/old_content/new_content/`: Substitution command to replace `old_content` with `new_content`.

*Example:*

Suppose you have an XML file with the following content:
```
<config>
    <server>
        <address>localhost:8080</address>
        <address>localhost:8081</address>
    </server>
</config>
```
To replace the port number `8080` with `8082` only in the first `<address>` tag, use:
```
bash
sed '/<address>.*localhost:8080/s/8080/8082/' file.xml
```
Result:
```
<config>
    <server>
        <address>localhost:8082</address>
        <address>localhost:8081</address>
    </server>
</config>
```

*Approach 2: Using a combination of patterns*

*Syntax:*
```
bash
sed '/<tag>.*pattern1.*pattern2/s/old_content/new_content/' document.txt
```
*Explanation:*

1. `/<tag>/`: Searches for the `<tag>` pattern.
2. `.*pattern1.*`: Matches any characters (including none) until `pattern1`, and then any characters (including none) until `pattern2`.
3. `s/old_content/new_content/`: Substitution command to replace `old_content` with `new_content`.

*Example:*

Suppose you have an XML file with the following content:
```
<config>
    <server>
        <address>localhost:8080</address>
        <address>localhost:8081</address>
        <address>localhost:8080</address>
    </server>
</config>
```
To replace the port number `8080` with `8082` only in the `<address>` tags that contain both `localhost` and `8080`, use:
```
bash
sed '/<address>.*localhost.*8080/s/8080/8082/' file.xml
```
Result:
```
<config>
    <server>
        <address>localhost:8082</address>
        <address>localhost:8081</address>
        <address>localhost:8082</address>
    </server>
</config>
```
Understanding the sed Command
Command Breakdown:
sed -i 's/\<\/PropertySet\>/<\/!--\/PropertySet-->/' /eclinicalWorks/ecwinstall/detail/vaibhav/AuditRepositoryConnections.xml

Let's break down this sed command step-by-step:
 * sed -i:
   * sed: This is the command itself, a powerful text stream editor.
   * -i: This option tells sed to modify the file in-place, meaning the changes will be directly applied to the original file.
 * 's/\<\/PropertySet\>/<\/!--\/PropertySet-->/':
   * s/: This initiates a substitution command.
   * \<\/PropertySet\>: This is the regular expression pattern to be searched for. It matches the literal string </PropertySet>.
   * <\/!--\/PropertySet-->: This is the replacement string. It replaces the matched pattern with a commented-out version of the same string.
What it does:
This command searches for the closing tag </PropertySet> within the specified XML file and replaces it with a commented-out version ``. This effectively comments out the entire PropertySet section in the XML file.
In essence, this command comments out a specific section of the XML file.
Caution:
 * Be cautious when using sed with the -i option. Once the file is modified in-place, there's no simple way to undo the changes. It's always recommended to back up the file before making significant modifications.
 * Regular expressions can be powerful but complex. If you're not familiar with regular expressions, it's easy to make mistakes. Always test your sed commands on a copy of the file before applying them to the original.
If you have any further questions or need more specific explanations, feel free to ask!








SELECT
    c.name AS PodName,
    b.name AS ServerName,
    b.ip,
    'EMR' AS EMR,
    c.id AS PodID
FROM
    appservers b
JOIN
    pod c ON b.podid = c.id
WHERE
    b.active = '1'
    AND c.active = '1'
    AND b.ip NOT LIKE '%:%'
ORDER BY
    c.name
LIMIT 1000000;

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
