<script type="text/javascript">
    function CallBtnUpload(e) {
        if (e.value !== "") {
            var validExtensions = [".jpg", ".jpeg", ".png", ".bmp", ".gif", ".webp"];
            var fileName = e.value.toLowerCase();
            var isValid = validExtensions.some(function (ext) {
                return fileName.endsWith(ext);
            });

            if (!isValid) {
                alert("Invalid file! Please select an image file (.jpg, .jpeg, .png, .bmp, .gif, .webp).");
                e.value = ""; // clear the invalid selection
                return false;
            }

            // continue with your button click if needed
            e.nextSibling.nextSibling.click();
        }
    }
</script>

foreach (GridViewRow row in GridView1.Rows)
{
    FileUpload fu = (FileUpload)row.FindControl("Attachments");
    if (fu != null && fu.HasFile)
    {
        string ext = Path.GetExtension(fu.FileName).ToLower();
        string[] validExts = { ".jpg", ".jpeg", ".png", ".bmp", ".gif", ".webp" };

        if (!validExts.Contains(ext))
        {
            MyMsgBox.Show("Invalid file type in row " + (row.RowIndex + 1) + "! Only image formats are allowed.");
            return;
        }

        // Save the valid file
        string savePath = Server.MapPath("~/upload/") + Path.GetFileName(fu.FileName);
        fu.SaveAs(savePath);
    }
}





i have this attachment input and i want to validate that only image files are acceptable other files are not
                                    <asp:TemplateField HeaderText="Attachments" SortExpression="Attachments" HeaderStyle-Width="5%"  >
                                            <ItemTemplate>
                                                <asp:FileUpload runat="server" ID="Attachments" Font-Size="X-Small" Font-Bold="true" onchange="CallBtnUpload(this);"  /> 
                                                <span class="m-0 ml-5" style="font-size:.6rem;color:red;">(Image Only.)</span> 

                                                <asp:BulletedList runat="server" ID="Attachment1" DisplayMode="LinkButton" Target="_blank" ForeColor="Blue" CssClass="attachment attachment-list" OnClick="Attachment1_Click" />

                                            </ItemTemplate>
                                        </asp:TemplateField>


 <script type="text/javascript">
     function CallBtnUpload(e) {

         alert("ok");
         if (e.value != "")
             e.nextSibling.nextSibling.click();

         // alert(fld);

         //if (!/(\.jpeg|\.png|\.bmp|\.jpg)$/i.test(fld)) {
         //    swal("Error !!!", "Invalid File, Please Select .jpeg, .png, .Jpg, .bmp File", "error");
         //    //alert("Invalid File, Please Select .bmp or .png or .img or .gif or .jpg or .jpeg File");      
         //    //fld.form.reset();
         //    fld.value = "";
         //    fld.focus();
         //    return false;
         //}
     }
 </script>
