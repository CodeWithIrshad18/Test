CREATE TABLE [dbo].[App_FaceVerification_Details] (
    [ID]                   UNIQUEIDENTIFIER DEFAULT (newid()) NOT NULL,
    [Pno]                  VARCHAR (6)      NULL,
    [DateAndTime]          DATETIME         DEFAULT (getdate()) NULL,
    [PunchIn_FailedCount]  INT              NULL,
    [PunchIn_Success]      BIT              NULL,
    [PunchOut_FailedCount] INT              NULL,
    [PunchOut_Success]     BIT              NULL,
    PRIMARY KEY CLUSTERED ([ID] ASC)
);


---Not registered Yet

select ema_perno,ema_ename,ema_dept_desc from SAPHRDB.dbo.T_EMPL_ALL 
where (ema_pyrl_area='JS' or ema_pyrl_area='ZZ') and ema_perno not in (select distinct Pno from App_Person) order by ema_dept_desc
