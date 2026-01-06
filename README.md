error :

Ans is neither a DataColumn nor a DataRelation for table .
Line 127:             string correctAns = row["Ans"].ToString();


void LoadSlides()
{
    DataTable dt = new DataTable();
    dt.Columns.Add("SlideType"); 
    dt.Columns.Add("ModuleId");
    dt.Columns.Add("ModuleName");
    dt.Columns.Add("Attachment");
    dt.Columns.Add("QuestionType");
    dt.Columns.Add("Question");
    dt.Columns.Add("Option1");
    dt.Columns.Add("Option2");
    dt.Columns.Add("Option3");
    dt.Columns.Add("Option4");
    dt.Columns.Add("QuestionImage");

    string cs = ConfigurationManager.ConnectionStrings["ApplicationServices"].ConnectionString;

    DataTable modules = new DataTable();
    DataTable attachments = new DataTable();
    DataTable questions = new DataTable();

    using (SqlConnection con = new SqlConnection(cs))
    {
        con.Open();


        new SqlDataAdapter(
            "SELECT ID, ModuleName, SerialNo FROM App_Module_Master WHERE IsActive = 1 ORDER BY SerialNo",
            con).Fill(modules);


        new SqlDataAdapter(
            "SELECT ModuleID, Attachments, SeqNo FROM App_Module_Attachments WHERE IsActive = 1 ORDER BY ModuleID, SeqNo",
            con).Fill(attachments);


        new SqlDataAdapter(
            @"SELECT Id, ModuleID, QuestionType, Question,
             Option1, Option2, Option3, Option4,
             QuestionImage, SeqNo
      FROM App_QuestionMaster
      WHERE IsActive = 1
      ORDER BY ModuleID, SeqNo",
            con).Fill(questions);
    }

    foreach (DataRow m in modules.Rows)
    {
        string moduleId = m["ID"].ToString();


        foreach (DataRow a in attachments.Select($"ModuleID = '{moduleId}'"))
        {
            DataRow r = dt.NewRow();
            r["SlideType"] = "ATTACHMENT";
            r["ModuleId"] = moduleId;
            r["ModuleName"] = m["ModuleName"];
            r["Attachment"] = a["Attachments"];
            dt.Rows.Add(r);
        }

        foreach (DataRow q in questions.Select($"ModuleID = '{moduleId}'"))
        {
            DataRow r = dt.NewRow();
            r["SlideType"] = "QUESTION";
            r["ModuleId"] = moduleId;
            r["ModuleName"] = m["ModuleName"];
            r["QuestionType"] = q["QuestionType"];
            r["Question"] = q["Question"];
            r["Option1"] = q["Option1"];
            r["Option2"] = q["Option2"];
            r["Option3"] = q["Option3"];
            r["Option4"] = q["Option4"];
            r["QuestionImage"] = q["QuestionImage"];
            dt.Rows.Add(r);
        }
    }

    rptSlides.DataSource = dt;
    rptSlides.DataBind();


}


protected void rptSlides_ItemDataBound(object sender, RepeaterItemEventArgs e)
{
    if (e.Item.ItemType != ListItemType.Item &&
        e.Item.ItemType != ListItemType.AlternatingItem)
        return;

    DataRowView row = (DataRowView)e.Item.DataItem;

    if (row["SlideType"].ToString() != "QUESTION")
        return;

  
    Image img = (Image)e.Item.FindControl("imgQuestion");
    if (row["QuestionImage"] != DBNull.Value)
    {
        img.ImageUrl = "~/Upload/" + row["QuestionImage"];
        img.Visible = true;
    }

    string qType = row["QuestionType"].ToString();
     string correctAns = row["Ans"].ToString();

    if (qType == "Objective")
    {
        RadioButtonList rbl = (RadioButtonList)e.Item.FindControl("rblOptions");
        rbl.Visible = true;
        rbl.Items.Clear();

        rbl.Items.Add(new ListItem(row["Option1"].ToString(), "1"));
        rbl.Items.Add(new ListItem(row["Option2"].ToString(), "2"));
        rbl.Items.Add(new ListItem(row["Option3"].ToString(), "3"));
        rbl.Items.Add(new ListItem(row["Option4"].ToString(), "4"));


        rbl.Attributes["data-answer"] = correctAns;
        rbl.Attributes["data-question-type"] = "objective";

        rbl.RepeatLayout = RepeatLayout.Flow;

       

       

    }



    if (qType == "Subjective")
    {
        TextBox txt = (TextBox)e.Item.FindControl("txtAnswer");
        txt.Visible = true;
    }
}
