        function interval() {
            
            var QuestionResultRecord = document.getElementById('<%=QuestionResultRecord.ClientID %>');
            var TotalTime = "MainContent_QuestionResultRecord_TotalTime_0";
            var TotalTimevalue = document.getElementById(TotalTime).value;
            //TotalTimevalue = TotalTimevalue * 60;
            TotalTimevalue = 60;
            var now = new Date();
            var time = now.getHours() + ':' + now.getMinutes() + ':' + now.setSeconds(now.getSeconds() + TotalTimevalue); 

//           
//                        var x = "JvEndTime";
//                        document.getElementById(x).value = time;



//        setInterval(function () {

//            var now = new Date();
//            //now.setMinutes(now.getMinutes() + 30);
//            var time = now.getHours() + ':' + now.getMinutes() + ':' + now.getSeconds();
//            //var time = now.getHours() + ':' + (now.getMinutes() + 30) + ':' + now.getSeconds();
//            var x = "JvEndTime";
//            document.getElementById(x).value = time;

//        }, 1000);

       // var count = TotalTimevalue;
            var count = 60;


        setInterval(function () {
            var now = new Date();
             var jspCall = "http://localhost:10019/index.aspx";
             // var jspCall = "http://169.0.16.242/QMS/index.aspx";
            //var jspCall = "http://10.0.168.40/QMS/index.aspx";
            //alert(TotalTimevalue);
            // count = count - 1;
            if (count <= TotalTimevalue && count > 0) {

                count = count - 1;

                document.getElementById("Timer1").value = count;

                if (document.getElementById("Timer1").value == 0) {
                   
                    PageMethods.test(onSuccess, onError);
                    alert("Time Up");
                


                }
            }
        }, 1000);


        }


    <asp:UpdatePanel ID="UpdatePanel1" runat="server">
            <ContentTemplate>
                
         
          <table border="1" cellspacing="0" cellpadding="0" style="border-color: #008000; table-layout:fixed; width:100%; height:400px; font-size:12px;">
              <tr>
              <td style="vertical-align:bottom; text-align:center;color:#0000FF; font-weight: bold;background-color: #999966;">
              <span style="color: #FFFFFF"><asp:Label ID="Label1" runat="server" Text="Label"></asp:Label>Questions</span>
              </td>
              </tr>
              <tr><td>
            <asp:Panel ID="Panel1" runat="server">
              <tr style="height:auto"><td style="vertical-align:top">
             
                 
            <fieldset style="height:30px;padding-top:0px ;padding-bottom:5px;" >
            <cc1:formcointainer ID="QuestionResultRecord" runat="server" AllowPaging="True" 
                AutoGenerateColumns="False" PageSize="1" 
                ShowHeader="False" Width="100%" DataSource="<%# PageRecordDataSet %>" 
                DataMember="App_QuistionResult_Master" DataKeyNames="ID" 
                onnewrowcreatingevent="QuestionResultRecord_NewRowCreatingEvent1"  
                    BindingErrorMessage="" GridLines="None" OnDataBound="QuestionResultRecord_DataBound" 
                     >
                
                <Columns>
                    <asp:TemplateField SortExpression="ID" Visible="False">
                        <ItemTemplate>
                            <asp:Label ID="ID" runat="server" ></asp:Label>
                        </ItemTemplate>
                    </asp:TemplateField>
                    <asp:TemplateField>
                    <ItemTemplate>
                  
                      
                        <table style="width: 100%;"> 
                            <tr>
                             <td style="color: #000000;" >
                               
                                  <span style="color: #FF0000; font-weight: bold; font-size: 12pt;"></span>
                                  </td>
                                <td class="ColumnColons">
                                  
                                </td>
                                <td class="ColumnField">
                                
                                 <asp:TextBox ID="Slno" runat="server" CssClass="ColumnFieldControls" Width="90px"  Visible="False" />
                                  
                                </td>
                                 
                               <td class="ColumnCaption"  >
                                 
                                </td>
                                 <td class="ColumnColons">
                                   
                                 </td>
                                 <td class="ColumnField">
                                    <asp:TextBox ID="CreatedBy" runat="server" CssClass="ColumnFieldControls" Width="90px" Visible="False" />

                                <td class="ColumnCaption">
                                                       
                                                    </td>
                                                    <td class="ColumnColons">
                                                        
                                                    </td>
                                                    <td class="ColumnField">
                                                        <asp:TextBox ID="EmpName" runat="server" CssClass="ColumnFieldControls" Width="195px" Visible="False" />
                                                       
                                                    </td>
                            </tr>

                            <tr>        
                                                                                                                
                                                    

                                                    <td class="ColumnCaption">
                                                        Exam Date 
                                                    </td>
                                                    <td class="ColumnColons">
                                                        :
                                                    </td>
                                                    <td class="ColumnField">
                                                        <asp:TextBox ID="ExamDate" runat="server" CssClass="ColumnFieldControls" Width="90px"  ReadOnly="true"/>
                                                        <asp:TextBox ID="MasterId" runat="server" CssClass="ColumnFieldControls" Visible="false" ></asp:TextBox>
                                                    </td>

                                                     
                                                     <td class="ColumnCaption">
                                                       Start Time 
                                                    </td>
                                                    <td class="ColumnColons">
                                                        :
                                                    </td>
                                                    <td class="ColumnField">
                                                        <asp:TextBox ID="StartTime" runat="server" CssClass="ColumnFieldControls" Width="80px"  ReadOnly="true" />
                                                        
                                                    </td>

                                                    <td class="ColumnCaption">
                                                       End Time 
                                                    </td>
                                                    <td class="ColumnColons">
                                                        :
                                                    </td>
                                                    <td class="ColumnField">
                                                       <%-- <input id="JvEndTime" type="text" name="JvEndTime" disabled="disabled" style="color: #000000" />--%>
                                                        <asp:TextBox ID="EndTime" runat="server" CssClass="ColumnFieldControls" Width="80px"  ReadOnly="true"  />
                                                       
                                                    </td>
                                                
                                   
                               <td class="ColumnCaption">
                                    Total Time (in Min)
                                </td>
                                <td class="ColumnColons">
                                    :
                                </td>
                                <td class="ColumnField">
                                     <asp:TextBox ID="TotalTime" runat="server"   Width="40px"  ForeColor="Black" ReadOnly="true" />
                                                      
                                </td>
                                <td class="ColumnCaption">
                                    Time Remaining (in Sec)
                                </td>
                                <td class="ColumnColons">
                                    :
                                </td>
                                <td class="ColumnField">
                                  <input id="Timer1" type="text" name="Timer1"  style="color: #000000;  width: 60px;"   readonly="readonly" />
                                      <asp:TextBox ID="Timer" runat="server" CssClass="ColumnFieldControls"  Enabled ="false"  Visible="False" />             
                                </td>

                                <td colspan="3" class="ColumnField" >
                                                  &nbsp;     
                                                         
                                                    </td>
                                
                                                
                            </tr>
                            <tr style="line-height: -30px"><td colspan="9" align="right">
                            <td>
                            <div>
                            <asp:Button ID="btnStart" runat="server" Text="Start" Width="50"   onclick="btnStart_Click" OnClientClick="if(confirm('Do you want to start Exam'))return interval();else return false; " Visible="False" />
                           
                            </div>
                            </td>
                            </tr>
                        
                       
                                          
                                                
                           
                        </table>
                </ItemTemplate>
                    </asp:TemplateField>
                </Columns>
                <PagerSettings Visible="False" />
            </cc1:formcointainer>
            </fieldset>
             
            </td>

            </tr>
            <tr>
            <td>
           
                                    
            </td>
            </tr>
             <tr id="QuestionChild" style="visibility:Visible">
                    <td style="vertical-align: bottom" >
                
                  <cc1:DetailsContainer ID="QuestionResultChild" runat="server" AutoGenerateColumns="False"
                            Width="100%" DataMember="App_QuestionResult" DataKeyNames="ID" DataSource="<%# PageRecordDataSet %>"
                            ShowHeaderWhenEmpty="True" 
                            onrowdatabound="QuestionResultChild_RowDataBound" >

                         
                            <Columns>
                            
                                <asp:TemplateField HeaderText="ID" SortExpression="ID" Visible="False">
                                    <ItemTemplate>
                                        <asp:Label ID="ID" runat="server"></asp:Label>
                                    </ItemTemplate> 
                                </asp:TemplateField>


                                 <asp:TemplateField HeaderText="MasterID" Visible="False">
                                    <ItemTemplate>
                                     <asp:Label ID="MasterID" runat="server" Width="100px"></asp:Label>
                                                                            
                                    </ItemTemplate>
                                </asp:TemplateField>


                                 <asp:TemplateField HeaderText="QuestionID" Visible="False">
                                    <ItemTemplate>
                                     <asp:Label ID="QuestionID" runat="server" Width="100px"></asp:Label>
                                                                         
                                    </ItemTemplate>
                                </asp:TemplateField>
                                <asp:TemplateField HeaderText="Sl. No." SortExpression="Slno" HeaderStyle-HorizontalAlign="Left" HeaderStyle-Width="3%">
                                    <ItemTemplate>
                                        <asp:TextBox ID="Slno" runat="server"  Width="85%"  ForeColor="#333333" ReadOnly="true"></asp:TextBox>
                                    </ItemTemplate> 
                                </asp:TemplateField>
                                <asp:TemplateField HeaderText="Questions" HeaderStyle-HorizontalAlign="Center" HeaderStyle-Width="89%" ItemStyle-VerticalAlign="Top" ItemStyle-HorizontalAlign="Left">
                                    <ItemTemplate>

                                     <asp:TextBox ID="Question" runat="server" Width="99%" ReadOnly="true"  ForeColor="Black" BorderColor="#0066FF" Font-Bold="True" TextMode="MultiLine" Wrap="True"></asp:TextBox>
                                     <asp:Image ID="QuestionImage" runat="server" Height="100px" Width="100px" ></asp:Image><br />
                                     <asp:TextBox ID="Ans1" runat="server" Width="99%"  ForeColor="Black" ReadOnly="true" ></asp:TextBox>
                                     <asp:TextBox ID="Ans2" runat="server" Width="99%"  ForeColor="Black" ReadOnly="true" ></asp:TextBox>
                                      <asp:TextBox ID="Ans3" runat="server" Width="99%"  ForeColor="Black" ReadOnly="true"></asp:TextBox>
                                       <asp:TextBox ID="Ans4" runat="server" Width="99%"  ForeColor="Black" ReadOnly="true" ></asp:TextBox>
                                    </ItemTemplate>
                                </asp:TemplateField>

                                 <asp:TemplateField HeaderText=" 1 " HeaderStyle-Width="2%" ItemStyle-HorizontalAlign="Left" >
                                    <ItemTemplate>
                                        <asp:RadioButton ID="RadioStornglyDisagree" runat="server" GroupName="Result" Width="90%" Checked="False" />
                                                                              
                                    </ItemTemplate>
                                     <HeaderStyle Width="55px" />
                                     <ItemStyle HorizontalAlign="Center" />
                                </asp:TemplateField>

                                <asp:TemplateField HeaderText=" 2 " HeaderStyle-Width="2%" ItemStyle-HorizontalAlign="Center" >
                                    <ItemTemplate>
                                        <asp:RadioButton ID="RadioDisagree" runat="server" GroupName="Result" Width="90%"/>
                                                                              
                                    </ItemTemplate>
                                    <HeaderStyle Width="55px" />
                                    <ItemStyle HorizontalAlign="Center" />
                                </asp:TemplateField>

                                <asp:TemplateField HeaderText=" 3 " HeaderStyle-Width="2%" ItemStyle-HorizontalAlign="Center" >
                                    <ItemTemplate>
                                        <asp:RadioButton ID="RadioAgreeDisagree" runat="server" GroupName="Result" Width="90%" />
                                                                              
                                    </ItemTemplate>
                                    <HeaderStyle Width="55px" />
                                    <ItemStyle HorizontalAlign="Center" />
                                </asp:TemplateField>

                                 <asp:TemplateField HeaderText=" 4 " HeaderStyle-Width="2%" ItemStyle-HorizontalAlign="Center" >
                                    <ItemTemplate>
                                        <asp:RadioButton ID="RadioAgree" runat="server" GroupName="Result" Width="90%" />
                                                                              
                                    </ItemTemplate>
                                     <HeaderStyle Width="55px" />
                                     <ItemStyle HorizontalAlign="Center" />
                                </asp:TemplateField>

                               


                                <asp:TemplateField HeaderText="CreatedBy"  Visible="False">
                                    <ItemTemplate>
                                        <asp:TextBox ID="CreatedBy" runat="server" ></asp:TextBox>
                                        
                                    </ItemTemplate>
                                </asp:TemplateField>

                                 
                               
                                <asp:BoundField DataField="Result" HeaderText="" 
                                    SortExpression="Result"   />

                                 
                                 <asp:TemplateField HeaderText="EngagementParameterID"  Visible="False">
                                    <ItemTemplate>
                                        <asp:Label ID="EngagementParameterID" runat="server" ></asp:Label>
                                        
                                    </ItemTemplate>
                                </asp:TemplateField>

                               
                            </Columns>
                            <HeaderStyle BackColor="#3756aa" ForeColor="White" />
                        </cc1:DetailsContainer>  
                              </td>
                </tr> 
                </asp:Panel>
              </td>  </tr>
            <tr><td  style="vertical-align:bottom; text-align:right  ">
            <div>
                <uc1:MyMsgBox ID="MyMsgBox" runat="server"/>
            </div>
           <div style="height:24px;padding-right:5px;background-color:Silver">
            
            <asp:Button ID="btnSave" runat="server" Text="Save" Width="50" OnClick="btnSave_Click" ValidationGroup="save"   />
            
            </div>
            </td></tr></table>
               
            <asp:Literal ID="Literal1" runat="server"></asp:Literal>
    </ContentTemplate>
</asp:UpdatePanel>
