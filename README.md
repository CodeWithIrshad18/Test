---for relationship 

=Switch(Fields!RELATION_CODE.Value="9H","Self",
Fields!RELATION_CODE.Value="1","Spouse",
Fields!RELATION_CODE.Value="2","Child",
Fields!RELATION_CODE.Value="11","Father",
Fields!RELATION_CODE.Value="12","Mother",
Fields!RELATION_CODE.Value="9A","Brother",
Fields!RELATION_CODE.Value="9B","Sister")

----for Gender

=Switch(Fields!GENDER_CODE.Value="1","Male",
Fields!GENDER_CODE.Value="2","Female")

----for child and Adult

=count(iif(Fields!AGE.Value<18,1,Nothing), "DataSet2")


=count(iif(Fields!AGE.Value>=18,1,Nothing),"DataSet2")
