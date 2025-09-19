Property or indexer '<anonymous type: Guid Id, int? Position, string Worksite, string Pno>.Worksite' cannot be assigned to -- it is read only

getting this error after apply this 

var query = from pw in context.AppPositionWorksites
            join ep in context.AppEmpPositions on pw.Position equals ep.Position
            select new
            {
                pw.Id,
                pw.Position,
                pw.Worksite,
                ep.Pno   // 👈 Now we also include Pno
            };

 
item.Worksite = names.Count > 0 ? string.Join(", ", names) : "(Invalid Worksite IDs)";

 item.Worksite = "(No Worksite)";
