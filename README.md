CONVERT(varchar, g.CreatedOn_GP, 103) AS [GatepassIssueDate],
IIF(DATEDIFF(DAY, '2025-07-15', ps.ESIChallanDate) < 0, 0, DATEDIFF(DAY, '2025-07-15', ps.ESIChallanDate)) AS [ESI Delay Days],
IIF(DATEDIFF(DAY, '2025-07-15', ps.PFChallanDate) < 0, 0, DATEDIFF(DAY, '2025-07-15', ps.PFChallanDate)) AS [PF Delay Days],
IIF(DATEDIFF(DAY, '2025-07-07', ow.PAYMENT_DATE) < 0, 0, DATEDIFF(DAY, '2025-07-07', ow.PAYMENT_DATE)) AS [Wage Delay Days]


CONVERT(varchar, g.CreatedOn_GP, 103) AS [GatepassIssueDate]
DATEDIFF(DAY, '2025-07-15', ps.ESIChallanDate) AS [ESI Delay Days],
DATEDIFF(DAY, '2025-07-15', ps.PFChallanDate) AS [PF Delay Days],
DATEDIFF(DAY, '2025-07-07', ow.PAYMENT_DATE) AS [Wage Delay Days],
