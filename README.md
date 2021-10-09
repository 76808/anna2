# anna2
<%@ Page Language="C#" MasterPageFile="~/MasterForms/MainMaster.Master" AutoEventWireup="true"
    CodeBehind="Education.aspx.cs" Inherits="eFIMO.JayamCBS.Education" Title="Untitled Page" %>

<asp:Content ID="Content1" ContentPlaceHolderID="ContentPlaceHolder1" runat="server">
    <style>
        #pager_center
        {
            width: 220px !important;
        }
        .ui-jqgrid .ui-paging-info
        {
            margin-right: 22px !important;
        }
    </style>

    <script type="text/javascript">

        $(function() {


            $(":button").button();
            $("#accordian").accordion({
                heightStyle: "content",
                active: 0,
                collapsible: true
            });
            jQuery("#txtname").focus();
            GetBusinessunitcode();
            $("#ddlhide").val('No');
        });




        //Auto generated code
        function GetBusinessunitcode() {
            var ob = new Object();
            ob.flag = "S";
            PageMethods.GetManpower(ob, onSucceess, onFailure);
            function onSucceess(r) {
                document.getElementById('txtcode').value = r;
            }
            function onFailure(r) {
                alert(r);
            }
            return false;
        }

        function leftTrim(element) {
            if (element)
                element.value = element.value.replace(/^\s+/, "");
        }

        //Save Begin
        function savevalues() {
            var ob = new Object();
            ob.MEDI_Name = $("#txtname").val();
            ob.Txn_ID = $("#hdnmedi").val();
            ob.MEDI_CODE1 = $("#txtcode").val();
            ob.UserID = $('#<%=lblUid.ClientID %>').val();
            ob.LatModifiedUser = $('#<%=lblUid.ClientID %>').val();
            ob.Flag = $('#<%=lblFlag.ClientID %>').val();
            ob.MEDI_Hide = $("#ddlhide").val();

            var res = requiredFieldValidator('txtcode,txtname', 'Enter Code,Enter Name');
            if (res == false) {
                document.getElementById('NoDetails').innerHTML = '';
                return false;

            }
            PageMethods.EducationDtls(ob, onSucceess, onFailure);

            function onSucceess(r) {
                alert(r);
                document.getElementById('NoDetails').innerHTML = '';
                document.getElementById('<%=lblFlag.ClientID %>').value = "I";
                ClearAll();
                //ShowGrid();
            }
            function onFailure(r) {
                document.getElementById('NoDetails').innerHTML = '';
                alert(r._message);
            }
            return false;
        }

        //save End

        //To Clear
        function ClearAll() {
            document.getElementById('txtname').value = "";
            document.getElementById('txtcode').value = "";
            $("#txtcode").attr("disabled", false);
            document.getElementById('NoDetails').innerHTML = '';
            $("#tblEducation").GridUnload();
            document.getElementById('<%=lblFlag.ClientID %>').value = "I";
            jQuery("#txtcode").focus();
            $("#ddlhide").val('No');
            GetBusinessunitcode();
            return false;
        }

        function isNumberNumericKey(evt) {
            var charCode = (evt.which) ? evt.which : event.keyCode;
            if ((charCode < 65 || charCode > 90) && (charCode < 97 || charCode > 122) && charCode != 8) {
                document.getElementById('NoDetails').innerHTML = 'Only  charcters are allowed.. ';
                return false;
            }
            else {
                //document.getElementById('NoDetails').innerHTML = '';
                return true;
            }
        }

        //Edit
        function EditRecords(Id) {
            var grid = $("#tblEducation");
            var rowData = grid.jqGrid('getRowData', Id);
            document.getElementById('txtname').value = rowData["MEDI_Name"];
            $("#txtcode").val(rowData.MEDI_CODE1);
            $("#ddlhide").val(rowData.MEDI_Hide);

            $("#txtcode").attr("disabled", "disabled");
            var medi = rowData["Txn_ID"];
            document.getElementById('hdnmedi').value = medi
            document.getElementById('<%=lblFlag.ClientID %>').value = "U";
            return false;
        }
        //Delete
        function DeleteRecords(Id) {
            var grid = $("#tblEducation");
            var rowData = grid.jqGrid('getRowData', Id);
            var medi = rowData["Txn_ID"];
            document.getElementById('hdnmedi').value = medi
            var UserMsg = window.confirm('Confirmation required!! Are You Sure Want to Delete Education: ' + rowData.MEDI_Name);
            if (UserMsg == true) {
                document.getElementById('<%=lblFlag.ClientID %>').value = "D";
                document.getElementById('txtname').value = rowData["MEDI_Name"];
                document.getElementById('txtcode').value = rowData["MEDI_Code"];
                ExistingUpdates();
            }
            else {
                document.getElementById('NoDetails').innerHTML = '';
                document.getElementById('<%=lblFlag.ClientID %>').value = "I";
            }

        }

        //To View
        function ShowGrid() {
            jQuery("#tblEducation").GridUnload();
            jQuery("#tblEducation").jqGrid({
                url: '<%= ResolveUrl("~/WebServices/JayamCBS/EducationCBS.asmx/ShowEdu") %>',
                contentType: "application/json; charset=utf-8",
                datatype: "json",
                height: '100%',
                rowNum: 10, width: '100%',
                cmTemplate: { sortable: false },
                rowList: [10, 20, 30],
                colNames: ['Edit', 'Id', 'Code', 'Code', 'Name', 'Hide'],
                colModel: [
              { name: 'Act', width: 40, index: 'Act', align: 'center', title: false },
                { name: 'Txn_ID', index: 'Txn_ID', width: 20, sorttype: "string", hidden: true },
                { name: 'MEDI_Code', index: 'MEDI_Code', width: 60, align: 'center', sorttype: "string", hidden: true },
                 { name: 'MEDI_CODE1', index: 'MEDI_CODE1', width: 60, align: 'Right', sorttype: "string" },
                { name: 'MEDI_Name', index: 'MEDI_Name', width: 200, align: 'Left', sorttype: "string" },
                { name: 'MEDI_Hide', index: 'MEDI_Hide', width: 60, align: 'Left', sorttype: "string" }

                ],
                pager: "#pager",
                viewrecords: true,
                rownumbers: true,
                altRows: true,
                caption: "Education",
                jsonReader: {
                    root: function(obj) { return obj.rows },
                    page: function(obj) { return obj.page },
                    total: function(obj) { return obj.total },
                    records: function(obj) { return obj.records },
                    id: "0",
                    cell: "",
                    repeatitems: false
                },
                gridComplete: function() {
                    var ids = jQuery("#tblEducation").jqGrid('getDataIDs');
                    if (ids.length == 0) {
                        //document.getElementById('NoDetails').innerHTML = 'No Education(s)';
                    }
                    else {
                        for (var i = 0; i < ids.length; i++) {
                            var cl = ids[i];
                            se = "<span tittle='Edit'  style='display:inline-block'  class='ui-icon ui-icon-pencil' onclick='EditRecords(" + cl + ")'></span> ";
                            ce = "<span tittle='Delete'  style='display:inline-block'  class='ui-icon ui-icon-trash' onclick='DeleteRecords(" + cl + ")'></span>";
                            jQuery("#tblEducation").jqGrid('setRowData', ids[i], { Act: se });
                        }
                        //document.getElementById('NoDetails').innerHTML = '';
                    }
                }

            });
            return false;
        }

        //Checking the Condition once updated Record shold not be modified
        function ExistingUpdates() {
            var mastid = $("#hdnmedi").val();
            var flag = "Education";

            var ob = new Object();
            PageMethods.EducationUpdatedDtls(mastid, flag, onSucceess, onFailure);

            function onSucceess(r) {
                var update = r[0].Msg;
                if (update == "T") {
                    alert('Already Operation Has Been Done');
                    ClearAll();
                    return false;
                }
                else {
                    savevalues();
                }
            }
            function onFailure(r) {
                document.getElementById('NoDetails').innerHTML = '';
                alert(r);
            }
            return false;
        }

        //Validation For Allow alphanumeric and space
        //        $(document).ready(function() {
        //            $("#txtname").keypress(function(event) {
        //                //  var inputValue = event.which;
        //                // allow letters and whitespaces only.
        //                var ignoredKeys = [8, 9, 37, 38, 39, 40];

        //                if (ignoredKeys.indexOf(event.which) >= 0 || (event.which >= 65 && event.which < 91) || (event.which > 96 && event.which < 123) || event.which === 32 || event.which === 0) {
        //                    return true;
        //                } else {
        //                    event.preventDefault();
        //                }
        //            });
        //        });

       
 
    </script>

    <%--<table class="table">
        <tr>
            <td class="tdheading">
                Education
            </td>
            <td align="right">
                <asp:SiteMapPath ID="SiteMapPath1" runat="server" Font-Names="Verdana" PathSeparator=" > ">
                    <CurrentNodeStyle ForeColor="#333333" />
                    <NodeStyle Font-Bold="True" ForeColor="#990000" />
                    <PathSeparatorStyle Font-Bold="True" ForeColor="#990000" />
                    <RootNodeStyle Font-Bold="True" ForeColor="#FF8000" />
                </asp:SiteMapPath>
            </td>
        </tr>
    </table>--%>
    <table class="table">
        <tr>
            <td>
                <div id="accordian">
                    <h3>
                        Education</h3>
                    <div>
                        <table>
                            <tr align="left">
                                <td class="tdtext" style="width: 90px">
                                    Code<span style="color: Red; font-size: small">*</span>
                                </td>
                                <td>
                                    <input type="text" class="textbox_disabled" disabled="disabled" id="txtcode" style="width: 150px" />
                                </td>
                            </tr>
                            <tr>
                                <td class="tdtext" style="width: 90px">
                                    Name<span style="font-size: 11pt; color: #ff4500">*</span>
                                </td>
                                <td>
                                    <input type="text" id="txtname" class="textbox" title="Enter Education Name" maxlength="50"
                                        autocomplete="off" style="width: 150px" />
                                </td>
                            </tr>
                            <tr>
                                <td class="tdtext">
                                    Hide<span style="color: Red; font-size: x-small">*</span>
                                </td>
                                <td>
                                    <select id="ddlhide" class="dropdownlist" style="width: 156px;">
                                        <option value="Yes">Yes</option>
                                        <option value="No">No</option>
                                    </select>
                                </td>
                            </tr>
                            <tr>
                                <td>
                                    <input type="hidden" name="Language" id="lblUid" runat="server" />
                                </td>
                                <td>
                                    <input type="hidden" id="lblFlag" runat="server" value="I" />
                                </td>
                                <td>
                                    <input type="hidden" id="hdnmedi" />
                                </td>
                            </tr>
                        </table>
                        <table class="table">
                            <tr>
                                <td>
                                    <span style="font-size: 8pt; color: #ff4500">Note: (*) Marked Fields are Mandatory.</span>
                                </td>
                            </tr>
                            <tr>
                                <td>
                                    <button id="btnSave" onclick="return  ExistingUpdates();" title="Tip:Select the Save Button">
                                        Save</button>
                                    <button id="btnView" onclick="return ShowGrid();" title="Tip:Display the Data">
                                        View</button>
                                    <button id="btnClear" onclick="return ClearAll();" title="Tip: Clears Data">
                                        Clear</button>
                                    <em><span style="color: #ff4500; font-weight: bolder; font-size: 12px;" id="NoDetails">
                                    </span></em>
                                </td>
                            </tr>
                            <tr>
                                <td>
                                    <table id="tblEducation">
                                    </table>
                                    <div id="pager">
                                    </div>
                                </td>
                            </tr>
                        </table>
                    </div>
                </div>
            </td>
        </tr>
    </table>
</asp:Content>
