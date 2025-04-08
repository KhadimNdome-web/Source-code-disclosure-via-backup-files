# Source-Code-Disclosure-Via-Backup-Files

This repository contains a collection of resources and write-ups related to discovering and exploiting source code disclosure vulnerabilities via backup files.

## Description

While exploring web security, I accessed a vulnerable web application at the URL: 

`https://0a6200c903dea49f812e3efd00ad0096.web-security-academy.net/`

I then tried appending `/robots.txt` to the URL to retrieve the site's robots.txt file, which can sometimes reveal sensitive information about the site's structure, including directories or files that are not meant to be publicly accessible.

In this case I found out that the robots.txt file is ordering the search engine to not index whatever data is included in the backup-path, as shown in the following:
`User-agent: *
Disallow: /backup`

I then used then the `/backup'` found in the text file and appended it at the end of the website url, similarly to how I first appended the `/robots.txt`, leading me to a file named ProductTemplate.java.bak.
This file in `https://0a6200c903dea49f812e3efd00ad0096.web-security-academy.net/backup/ProductTemplate.java.bak` is leaking some source in the java language, which could be reported as a bug.
After reading and analizing the rest of the code in order to find more sensitive informations, a particular section gained my attention:

`ConnectionBuilder connectionBuilder = ConnectionBuilder.from(
                "org.postgresql.Driver",
                "postgresql",
                "localhost",
                5432,
                "postgres",
                "postgres",
                "reb1t152594ghdd0ym2muvcjuxphmkfy"
        ).withAutoCommit();
        try`

The code shows a connection trying to be established sending data to a domain named `org.postgresql.Driver`. 
Not knowing what PostgreSQL is, i researched into it to find out it´s a type of database engine, that in this case is sent to `localhost` and indicating the port `5432`, followes supposedly by a username and database name respectively("postgres"), and finally something that semems to be a password (``reb1t152594ghdd0ym2muvcjuxphmkfy`), revealing other sensitive informations in the database that could be accessed by external malicious actors.
