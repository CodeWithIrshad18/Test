var query = from pw in context.AppPositionWorksites
            join ep in context.AppEmpPositions on pw.Position equals ep.Position into epg
            from ep in epg.DefaultIfEmpty()   // left join in case no match
            select new PositionWorksiteViewModel
            {
                Id = pw.Id,
                Position = pw.Position,
                Worksite = pw.Worksite,   // initially CSV ids
                Pno = ep.Pno
            };
foreach (var item in pagedData)
{
    if (!string.IsNullOrWhiteSpace(item.Worksite))
    {
        var ids = item.Worksite
            .Split(',', StringSplitOptions.RemoveEmptyEntries)
            .Select(id => id.Trim().ToLower())
            .ToList();

        var names = ids
            .Where(id => worksiteDictionary.ContainsKey(id))
            .Select(id => worksiteDictionary[id])
            .ToList();

        item.Worksite = names.Count > 0 ? string.Join(", ", names) : "(Invalid Worksite IDs)";
    }
    else
    {
        item.Worksite = "(No Worksite)";
    }
}




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
