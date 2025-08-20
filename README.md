var pnoSpecificKpis = teams
    .Where(t => t.Pno != null)
    .GroupBy(t => t.Pno)
    .ToDictionary(
        group => group.Key,
        group => context.AppDareToTryMasters
            .Where(option => option.PersonalNumber == group.Key)
            .ToList()
    );

var pnoSpecificKpis = teams
    .GroupBy(t => t.Pno ?? "UNKNOWN")
    .ToDictionary(
        group => group.Key,
        group => context.AppDareToTryMasters
            .Where(option => option.PersonalNumber == group.Key)
            .ToList()
    );

var pnoSpecificKpis = teams
    .ToLookup(t => t.Pno)
    .ToDictionary(
        group => group.Key, // can still be null if you access directly
        group => context.AppDareToTryMasters
            .Where(option => option.PersonalNumber == group.Key)
            .ToList()
    );


var pnoSpecificKpis = teams
.GroupBy(t => t.Pno)
.ToDictionary(
	group => group.Key,
	group => context.AppDareToTryMasters
		.Where(option => option.PersonalNumber == group.Key) 
		.ToList()
);

getting this error why?

ArgumentNullException: Value cannot be null. (Parameter 'key')
