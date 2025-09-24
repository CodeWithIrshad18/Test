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
