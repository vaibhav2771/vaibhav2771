DELIMITER $$

CREATE PROCEDURE InsertAdhocPatchAndCookbook(
    IN p_booktype VARCHAR(255),
    IN p_dispname VARCHAR(255),
    IN p_usrgroup VARCHAR(255),
    IN p_description TEXT,
    IN p_templatename VARCHAR(255),
    IN p_destfolder VARCHAR(255)
)
BEGIN
    -- Check if the booktype already exists in adhocpatches
    IF EXISTS (SELECT 1 FROM adhocpatches WHERE booktype = p_booktype) THEN
        SIGNAL SQLSTATE '45000' SET MESSAGE_TEXT = 'Error: Booktype already exists in adhocpatches.';
    END IF;

    -- Check if the templatename already exists in cookbookdetails
    IF EXISTS (SELECT 1 FROM cookbookdetails WHERE templatename = p_templatename) THEN
        SIGNAL SQLSTATE '45000' SET MESSAGE_TEXT = 'Error: Template name already exists in cookbookdetails.';
    END IF;

    -- Insert into adhocpatches
    INSERT INTO adhocpatches (booktype, dispname, usrgroup, delflag, uploadtime, enabledmodule, DESCRIPTION)
    VALUES (p_booktype, p_dispname, p_usrgroup, 0, NOW(), 'misc', p_description);

    -- Insert into cookbookdetails
    INSERT INTO cookbookdetails (templatename, destfolder, podid, booktype, description, active)
    VALUES (p_templatename, p_destfolder, 0, p_booktype, p_description, 1);
END$$

DELIMITER ;






CALL InsertAdhocPatchAndCookbook(
    'adhocserverIR5586541',
    'eRX Search Cloud IR 5586541',
    'admin,misc',
    'IR-5586541 update searchcloud.scheduling.enabled=true in application.properties.',
    'adhocserverIR5586541',
    'tomcatupgradecookbooks'
);
