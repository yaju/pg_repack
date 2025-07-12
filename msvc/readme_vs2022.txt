Why use pg_repack.2022.sln
pg_repack.2022.sln allows you to build easily pg_repack with a version of EntrepriseDB's PostgreSQL comming from EntrepriseDB installer.

How to build with Microsoft Visual C++ 2022 with pg_repack.2022.sln


You might need:
  1. Register PostgreSQL directory to your environment.
        - PostgreSQL64 for 64 bits version
  2. Resolve redefinitions of ERROR macro.
  3. Modify pg_repack.sql.in

----
1. Register PostgreSQL directory to your environment.

Add to your environment variables :
  - PostgreSQL64 for 64 bits version with the path of your postgreSQL 64 bits installation directory


The directory configuration options are found in:
  Tool > Option > Projects and Solutions > VC++ directory

You might need to add the following directories:
  into "include files"
    - $(PostgreSQL64)\include\server\port\win32_msvc
    - $(PostgreSQL64)\include\server\port\win32
    - $(PostgreSQL64)\include\server
    - $(PostgreSQL64)\include\internal
    - $(PostgreSQL64)\include
  into "library files"
    - $(PostgreSQL64)\lib


----
2. Resolve redefinitions of ERROR macro.

It might be a bad manner, but I'll recommend to modify your wingdi.h.

--- wingdi.h       2008-01-18 22:17:42.000000000 +0900
+++ wingdi.fixed.h 2010-03-03 09:51:43.015625000 +0900
@@ -101,11 +101,10 @@
 #endif // (_WIN32_WINNT >= _WIN32_WINNT_WINXP)

 /* Region Flags */
-#define ERROR               0
+#define RGN_ERROR           0
 #define NULLREGION          1
 #define SIMPLEREGION        2
 #define COMPLEXREGION       3
-#define RGN_ERROR ERROR

 /* CombineRgn() Styles */
 #define RGN_AND             1

----
3. Change REPACK_VERSION to your version number
Rename pg_repack.control.in and replace the placeholder REPACK_VERSION 
in its contents with the actual version number.
For example, update REPACK_VERSION to 1.5.2.

To prepare for the release, rename pg_repack.sql.in to pg_repack--(VersionNo).sql, 
replacing (VersionNo) with the actual version number.
For example, rename it to pg_repack--1.5.2.sql.

----
4. Modify pg_repack.sql.in

It is not possible to create tables with OIDs on PostgreSQL 12 or later.
Replace relhasoids column with false.

    -- table oid
    SELECT 'oids = ' ||
-       CASE WHEN relhasoids
+       CASE WHEN false
            THEN 'true'
            ELSE 'false'
        END
    FROM pg_class



