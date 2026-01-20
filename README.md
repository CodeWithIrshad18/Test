SELECT ms.ModuleName, QM.QuestionType, QM.Question,
                     QM.Option1, QM.Option2, QM.Option3, QM.Option4,QM.Ans                   
              FROM App_QuestionMaster QM
               left join App_Module_Master ms
                     on ms.ID = QM.ModuleID                   
              WHERE QM.IsActive = 1

output : 

ModuleName	QuestionType	Question	Option1	Option2	Option3	Option4	Ans
CHRO Message	Objective	Which component is the lightweight, cross-platform web server included by default in ASP.NET Core applications? 	IIS	Apache	Kestrel	Nginx	3
Milestone1	Objective	Which of the following is NOT a feature of ASP.NET?	Web Forms	MVC	Routing	React.js	4
Milestone1	Subjective	Tell me about yourself.					1
Milestone4	Subjective	Explain Dependency Injection in .NET Core					1
Milestone1	Objective	ASP.NET applications can be hosted on which of the following?	Windows Server	Linux	MacOS	All of the above	1
Instructions	Objective	Which language is primarily used for ASP.NET web application development?	Java	C#	Python	Ruby	2
Home Page	Subjective	What is .NET Core? why we prefer .NET Core in place of .NET?					1
Milestone1	Objective	What does the 'ASP' in ASP.NET stand for?	Active Server Pages	Application Service Pages	Active Service Pages	Application Server Pages	1



 select * from ASP_User_Response

Output

ID	UserID	ModuleID	QuestionID	SelectedOption	Subjective_Answer	IsCorrect	Answered_On	Created_By	Created_On
1A978AF7-623D-4E99-840A-12A5C2909427	159445	12ED0FE6-D3D3-4A13-BCCB-6E7272FDADF7	C7124DD4-AAEE-4348-9F9B-93DD4B202E97	NULL	testinggg	0	2026-01-20 12:28:16.060	159445	2026-01-20 12:28:16.060
82CCF12B-0406-4D8D-A7C2-4C96385E88AD	159445	FCB7ADB8-E802-4957-9410-C3B4B354CDDA	187D3FEA-9F26-4688-AF8D-1A200599BAF0	3	NULL	0	2026-01-20 12:28:45.343	159445	2026-01-20 12:28:45.343
D885D458-9BC0-4865-AE75-7014E7891458	159445	041AC05D-822D-4597-9F89-C0389A56AD13	57FF2291-5528-40F8-92CB-060575E54ACF	2	NULL	0	2026-01-20 12:28:21.490	159445	2026-01-20 12:28:21.490
7D94C357-F25E-4D79-8013-8471177BB3EE	159445	8974E29B-269D-4707-BB44-39CA88285D37	F8366C72-6EEE-45A5-8290-59436F6DF2B8	2	NULL	1	2026-01-20 12:28:28.713	159445	2026-01-20 12:28:28.713
5CBF7ED4-C41A-4DC3-B738-866640F50EC3	159445	FCB7ADB8-E802-4957-9410-C3B4B354CDDA	403712E7-C646-480E-B3B3-C8D1565971A1	2	NULL	0	2026-01-20 12:28:59.463	159445	2026-01-20 12:28:59.463
5DBEDAD3-A002-47E7-86F3-A747F3C46662	159445	45F01FDE-0D73-46E1-8D34-9976B3C4E1DE	1E5731E4-0D3B-4FBB-B243-29FAB758D29F	NULL	Testing	0	2026-01-20 12:29:33.450	159445	2026-01-20 12:29:33.450
CFF8D134-2B31-4C79-B367-C916AE32ABCD	159445	FCB7ADB8-E802-4957-9410-C3B4B354CDDA	EBB81265-CE4B-44BA-8D88-4C5E94EA8837	1	NULL	1	2026-01-20 12:29:02.453	159445	2026-01-20 12:29:02.453
EF203DE3-4B1E-40AF-A4E5-D510A8EF1D4C	159445	FCB7ADB8-E802-4957-9410-C3B4B354CDDA	7B779D2B-B79C-44AB-98BE-1FAD9A2C3D08	NULL	Hello, I am Irshad	0	2026-01-20 12:29:25.280	159445	2026-01-20 12:29:25.280


please make a query after combine and 
