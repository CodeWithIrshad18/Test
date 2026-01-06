my query 

SELECT 
    M.ID AS ModuleId,
    M.ModuleName,

    A.Attachments,
    A.SeqNo AS AttachmentSeq,

    Q.Id AS QuestionId,
    Q.QuestionType,
    Q.Question,
    Q.Option1,
    Q.Option2,
    Q.Option3,
    Q.Option4,
    Q.QuestionImage,
    Q.SeqNo AS QuestionSeq

FROM App_Module_Master M

LEFT JOIN App_Module_Attachments A
    ON A.ModuleID = M.ID AND A.IsActive = 1

LEFT JOIN App_QuestionMaster Q
    ON Q.ModuleID = M.ID AND Q.IsActive = 1

WHERE M.IsActive = 1
ORDER BY 
    M.SerialNo,
    A.SeqNo,
    Q.SeqNo;


and data 

ModuleId	ModuleName	Attachments	AttachmentSeq	QuestionId	QuestionType	Question	Option1	Option2	Option3	Option4	QuestionImage	QuestionSeq
12ED0FE6-D3D3-4A13-BCCB-6E7272FDADF7	Home Page	e29237c4-4d00-46d5-ad11-9c9b591462a4Screenshot 2025-12-17 170622 1.png	1	C7124DD4-AAEE-4348-9F9B-93DD4B202E97	Subjective	What is .NET Core? why we prefer .NET Core in place of .NET?					NULL	1
041AC05D-822D-4597-9F89-C0389A56AD13	CHRO Message	046326a2-f950-4f7a-860a-ee8ddd73b011Screenshot 2025-12-17 181535.png	2	57FF2291-5528-40F8-92CB-060575E54ACF	Objective	Which component is the lightweight, cross-platform web server included by default in ASP.NET Core applications? 	IIS	Apache	Kestrel	Nginx	NULL	1
8974E29B-269D-4707-BB44-39CA88285D37	Instructions	cb1a0511-944b-4953-ab57-4bab0013eb07Screenshot 2025-12-17 182925.png	3	F8366C72-6EEE-45A5-8290-59436F6DF2B8	Objective	Which language is primarily used for ASP.NET web application development?	Java	C#	Python	Ruby	NULL	1
FCB7ADB8-E802-4957-9410-C3B4B354CDDA	Milestone1	1064087b-9d64-48f0-9c91-15d075084a21Screenshot 2025-12-18 095419.png	1	187D3FEA-9F26-4688-AF8D-1A200599BAF0	Objective	Which of the following is NOT a feature of ASP.NET?	Web Forms	MVC	Routing	React.js	NULL	1
FCB7ADB8-E802-4957-9410-C3B4B354CDDA	Milestone1	1064087b-9d64-48f0-9c91-15d075084a21Screenshot 2025-12-18 095419.png	1	403712E7-C646-480E-B3B3-C8D1565971A1	Objective	What does the 'ASP' in ASP.NET stand for?	Active Server Pages	Application Service Pages	Active Service Pages	Application Server Pages	NULL	2
FCB7ADB8-E802-4957-9410-C3B4B354CDDA	Milestone1	1064087b-9d64-48f0-9c91-15d075084a21Screenshot 2025-12-18 095419.png	1	EBB81265-CE4B-44BA-8D88-4C5E94EA8837	Objective	ASP.NET applications can be hosted on which of the following?	Windows Server	Linux	MacOS	All of the above	NULL	3
FCB7ADB8-E802-4957-9410-C3B4B354CDDA	Milestone1	1064087b-9d64-48f0-9c91-15d075084a21Slide14 1.PNG	2	187D3FEA-9F26-4688-AF8D-1A200599BAF0	Objective	Which of the following is NOT a feature of ASP.NET?	Web Forms	MVC	Routing	React.js	NULL	1
FCB7ADB8-E802-4957-9410-C3B4B354CDDA	Milestone1	1064087b-9d64-48f0-9c91-15d075084a21Slide14 1.PNG	2	403712E7-C646-480E-B3B3-C8D1565971A1	Objective	What does the 'ASP' in ASP.NET stand for?	Active Server Pages	Application Service Pages	Active Service Pages	Application Server Pages	NULL	2
FCB7ADB8-E802-4957-9410-C3B4B354CDDA	Milestone1	1064087b-9d64-48f0-9c91-15d075084a21Slide14 1.PNG	2	EBB81265-CE4B-44BA-8D88-4C5E94EA8837	Objective	ASP.NET applications can be hosted on which of the following?	Windows Server	Linux	MacOS	All of the above	NULL	3
FCB7ADB8-E802-4957-9410-C3B4B354CDDA	Milestone1	1064087b-9d64-48f0-9c91-15d075084a21Screenshot 2025-12-18 095343.png	3	187D3FEA-9F26-4688-AF8D-1A200599BAF0	Objective	Which of the following is NOT a feature of ASP.NET?	Web Forms	MVC	Routing	React.js	NULL	1
FCB7ADB8-E802-4957-9410-C3B4B354CDDA	Milestone1	1064087b-9d64-48f0-9c91-15d075084a21Screenshot 2025-12-18 095343.png	3	403712E7-C646-480E-B3B3-C8D1565971A1	Objective	What does the 'ASP' in ASP.NET stand for?	Active Server Pages	Application Service Pages	Active Service Pages	Application Server Pages	NULL	2
FCB7ADB8-E802-4957-9410-C3B4B354CDDA	Milestone1	1064087b-9d64-48f0-9c91-15d075084a21Screenshot 2025-12-18 095343.png	3	EBB81265-CE4B-44BA-8D88-4C5E94EA8837	Objective	ASP.NET applications can be hosted on which of the following?	Windows Server	Linux	MacOS	All of the above	NULL	3
FCB7ADB8-E802-4957-9410-C3B4B354CDDA	Milestone1	1064087b-9d64-48f0-9c91-15d075084a21Screenshot 2025-12-18 095646.png	4	187D3FEA-9F26-4688-AF8D-1A200599BAF0	Objective	Which of the following is NOT a feature of ASP.NET?	Web Forms	MVC	Routing	React.js	NULL	1
FCB7ADB8-E802-4957-9410-C3B4B354CDDA	Milestone1	1064087b-9d64-48f0-9c91-15d075084a21Screenshot 2025-12-18 095646.png	4	403712E7-C646-480E-B3B3-C8D1565971A1	Objective	What does the 'ASP' in ASP.NET stand for?	Active Server Pages	Application Service Pages	Active Service Pages	Application Server Pages	NULL	2
FCB7ADB8-E802-4957-9410-C3B4B354CDDA	Milestone1	1064087b-9d64-48f0-9c91-15d075084a21Screenshot 2025-12-18 095646.png	4	EBB81265-CE4B-44BA-8D88-4C5E94EA8837	Objective	ASP.NET applications can be hosted on which of the following?	Windows Server	Linux	MacOS	All of the above	NULL	3
45F01FDE-0D73-46E1-8D34-9976B3C4E1DE	Milestone4	d270587a-4680-477a-ad4f-78de8be1bfe632f33178-1b3c-4a39-b860-bf694271d513_02-12-2025_06-35-37_Screenshot 2025-12-02 120431.png	5	1E5731E4-0D3B-4FBB-B243-29FAB758D29F	Subjective	testing					NULL	1
