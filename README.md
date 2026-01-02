private void InsertLog(string type, string createdBy, string message)
{
    string cs = ConfigurationManager.ConnectionStrings["DefaultConnection"].ConnectionString;

    using (SqlConnection con = new SqlConnection(cs))
    using (SqlCommand cmd = new SqlCommand(
        @"INSERT INTO UserActionLog (Type, CreatedBy, CreatedOn, Message)
          VALUES (@Type, @CreatedBy, GETDATE(), @Message)", con))
    {
        cmd.Parameters.AddWithValue("@Type", type);
        cmd.Parameters.AddWithValue("@CreatedBy", createdBy);
        cmd.Parameters.AddWithValue("@Message", message);

        con.Open();
        cmd.ExecuteNonQuery();
    }
}


protected void ResetBtn_Click(object sender, EventArgs e)
{
    var ser1 = new SAPPIPO_PasswordReset.SI_OS_ZSAP_PASSWORD_RESETService();
    ser1.Credentials = new NetworkCredential("TSUISLPIO_TEST", "Support@2051");
    ser1.Timeout = 600000;

    string response = ser1.SI_OS_ZSAP_PASSWORD_RESET(UserId.Text);

    InsertLog(
        type: "PasswordReset",
        createdBy: UserId.Text,
        message: response
    );

    MyMsgBox.show(UserLogin.Control.MyMsgBox.MessageType.Errors, response);
}
 
protected void UnlockBtn_Click(object sender, EventArgs e)
{
    var ser1 = new SAPPIPO_UserUnlock.SI_OS_ZSAP_USER_ID_UNLOCKService();
    ser1.Credentials = new NetworkCredential("TSUISLPIO_TEST", "Support@2051");
    ser1.Timeout = 600000;

    string response = ser1.SI_OS_ZSAP_USER_ID_UNLOCK(UserId.Text);

    InsertLog(
        type: "UserUnlock",
        createdBy: UserId.Text,
        message: response
    );

    MyMsgBox.show(UserLogin.Control.MyMsgBox.MessageType.Errors, response);
}



 
 
 
 protected void ResetBtn_Click(object sender, EventArgs e)
        {
            SAPPIPO_PasswordReset.SI_OS_ZSAP_PASSWORD_RESETService ser1 =new SAPPIPO_PasswordReset.SI_OS_ZSAP_PASSWORD_RESETService();
            ser1.Credentials = new System.Net.NetworkCredential("TSUISLPIO_TEST", "Support@2051");
            ser1.Timeout = 600000;
            string s = ser1.SI_OS_ZSAP_PASSWORD_RESET(UserId.Text);

            MyMsgBox.show(UserLogin.Control.MyMsgBox.MessageType.Errors, s);
        }

        protected void UnlockBtn_Click(object sender, EventArgs e)
        {
            SAPPIPO_UserUnlock.SI_OS_ZSAP_USER_ID_UNLOCKService ser1 = new SAPPIPO_UserUnlock.SI_OS_ZSAP_USER_ID_UNLOCKService();
            ser1.Credentials = new System.Net.NetworkCredential("TSUISLPIO_TEST", "Support@2051");
            ser1.Timeout = 600000;
            string s = ser1.SI_OS_ZSAP_USER_ID_UNLOCK(UserId.Text);

            MyMsgBox.show(UserLogin.Control.MyMsgBox.MessageType.Errors, s);
        }


 [ID]        UNIQUEIDENTIFIER DEFAULT (newid()) NOT NULL,
    [Type]      VARCHAR (25)     NULL,
    [CreatedBy] VARCHAR (6)      NULL,
    [CreatedOn] DATETIME         NULL,
    [Message]   VARCHAR (100)    NULL,
    PRIMARY KEY CLUSTERED ([ID] ASC)
