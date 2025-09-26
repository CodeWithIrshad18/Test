using System;
using System.Data;

public partial class app_input_bonus_complaince_entry : System.Web.UI.Page
{
    protected void Page_Load(object sender, EventArgs e)
    {
        if (!IsPostBack)
        {
            BindGrid(""); // initial load
        }
    }

    protected void ddlyear_SelectedIndexChanged(object sender, EventArgs e)
    {
        string selectedYear = ddlyear.SelectedValue;
        BindGrid(selectedYear);
    }

    private void BindGrid(string year)
    {
        // Sample data (replace with DB query)
        DataTable dt = new DataTable();
        dt.Columns.Add("Year");
        dt.Columns.Add("Vendor");
        dt.Columns.Add("Amount");

        dt.Rows.Add("2024-2025", "Vendor A", "1000");
        dt.Rows.Add("2025-2026", "Vendor B", "2000");
        dt.Rows.Add("2026-2027", "Vendor C", "3000");

        if (!string.IsNullOrEmpty(year))
        {
            DataView dv = dt.DefaultView;
            dv.RowFilter = $"Year = '{year}'";
            GridView1.DataSource = dv;
        }
        else
        {
            GridView1.DataSource = dt;
        }

        GridView1.DataBind();
    }
}




'ASP.app_input_bonus_complaince_entry_aspx' does not contain a definition for 'ddlyear_SelectedIndexChanged' and no extension method 'ddlyear_SelectedIndexChanged' accepting a first argument of type 'ASP.app_input_bonus_complaince_entry_aspx' could be found (are you missing a using directive or an assembly reference?)
