existing data fetching  :
<input name="ctl00$MainContent$Road_Record$ctl02$Location" type="text" value="22.80474894876291,86.17889502642825; 22.804961587337772,86.17947438357555; 22.804452243220236,86.1796782314607; 22.80430883530968,86.17908814547734; 22.80474894876291,86.17889502642825" id="MainContent_Road_Record_Location_0" class="form-control form-control-sm col-12 input">

textbox : 
  <div class="form-group col-md-3 mb-1">
      <label for="Location" class="m-0 mr-2 p-0 col-form-label-sm  font-weight-bold fs-6" >Location:<span class="text-danger">*</span></label>
      <asp:TextBox ID="Location" runat="server" CssClass="form-control form-control-sm col-12 input"  />
  </div> 

now i want to draw the coordinates for existing data when user clicks on Location textbox Map automatically draw that part with coordinates 
