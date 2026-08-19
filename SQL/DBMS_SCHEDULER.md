Yes. You can schedule a procedure in Oracle Autonomous Transaction Processing (ATP) using DBMS_SCHEDULER.

For example, if you have a procedure:

   CREATE OR REPLACE PROCEDURE process_orders
   AS
   BEGIN
      -- Business logic
   END;
   /

You can schedule it to run every day at 2:00 AM:

   BEGIN
      DBMS_SCHEDULER.CREATE_JOB (
         job_name        => 'PROCESS_ORDERS_JOB',
         job_type        => 'STORED_PROCEDURE',
         job_action      => 'PROCESS_ORDERS',
         start_date      => SYSTIMESTAMP,
         repeat_interval => 'FREQ=DAILY;BYHOUR=2;BYMINUTE=0;BYSECOND=0',
         enabled         => TRUE
      );
   END;
   /

Interview
---
1.In ATP can we schedule a procedure?
   Yes. Oracle ATP supports scheduling stored procedures using the DBMS_SCHEDULER package. We can create jobs to run procedures immediately, at a specific time, or on a recurring schedule. This is useful for long-running batch processing because the work executes in the background without requiring OIC to wait for completion, helping avoid OIC's synchronous timeout limits.