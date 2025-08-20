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
