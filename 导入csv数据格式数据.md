1. Excel数据格式处理
    对于多个sheet列，需要合并为一列
    ···
    Sub main()
    For Each sh In Sheets
    If sh.Name <> "总表" Then
    i = sh.Range("V65536").End(3).Row
    k = Range("A65536").End(3).Row
    sh.Range("A1:V" & i).Copy Range("A" & k + 1)
    End If
    Next
    End Sub
    ···
    注：总表：要copy的表的sheet名
        V65536：最后一列有数据的列
        A1：V：从第一列copy到第V列

···      
Sub 合并当前工作薄下的所有工作表()
Application.ScreenUpdating = False
For j = 1 To Sheets.Count
    If Sheets(j).Name <> ActiveSheet.Name Then
        X = Range("A65536").End(xlUp).Row + 1
        MsgBox X, vbInformation, "提示"
        Sheets(j).UsedRange.Copy Cells(X, 1)
    End If
Next
Range("B1").Select
Application.ScreenUpdating = True
MsgBox "合并完成！", vbInformation, "提示"
End Sub
···

2. excel数据格式转csv数据格式
    选择excel文件，点击另存为csv数据格式

3. mongoDB执行命令
    找到mongoDB安装目录，进入bin所在文件夹，执行：
    mongoimport.exe -d 数据库名 -c 表名 --type csv --headerline --ignoreBlanks --file 导入文件所在的路径

也可指定数据类型导入：
mongoimport.exe -d 数据库名 -c 表名 --type csv --headerline --ignoreBlanks --fields "name.string(),birthdate.date(2006-01-02),contacted.boolean(),followerCount.int32(),user thumbnail.binary(base64)" --file /example/file.csv

mongoimport.exe -d MEMBERDB -c member_test --type csv --columnsHaveTypes --fields "system_id,list_num,card_num.string(),member_img,guest_name,register_name,id_name,custom_name,create_card_time,sex,age,member_intergral,member_birth,levels,wx_register_address,id_register_address,member_phone,member_tel,paper_type,paper_num,id_num,email,hobby,is_have_children,children_birth,add_time,last_update_people,last_update_time,member_sources,address,isAuthentication,member_label,weixin_id,nation,wx_name,wx_sex,language,area,common_address,postcodes,is_follow,follow_time,position,company,park_nums,papers,create_time,isWxRegister.boolean(),organization" --file E:/会员数据处理/会员2015.csv


mongoimport.exe -d MEMBERDB -c member_test --type csv --columnsHaveTypes --fields "card_num.string(),member_name,create_card_time,sex,age,member_intergral,member_birth,levels,wx_register_address,id_register_address,member_phone,member_tel,paper_type,paper_num,email,hobby,is_have_children,children_birth,add_time,last_update_people,last_update_time" --file E:/会员数据处理/会员2015.csv

mongoimport.exe -d MEMBERDB -c member_test --type csv --headerline --file E:/会员数据处理/会员2015.csv

注：中文乱码，可以选择用editPlus打开csv软件，更改编码方式为utf-8，保存更改，再进行导入即可。

function formatNumber(nubmer) {
    return nubmer < 10 ? '0' + nubmer : nubmer;
}

function getTimeString(dateStr){
    var reg = new RegExp( '-' , "g" )
    dateStr = dateStr.replace(reg , '/' );
    date = new Date(dateStr);
    year = date.getYear() + 1900 - 2000;
    month = formatNumber(date.getMonth() + 1);
    day = formatNumber(date.getDate());
    hour = formatNumber(date.getHours());
    minute = formatNumber(date.getMinutes());
    second = formatNumber(date.getSeconds());
    
    return '' + year + month + day + hour + minute + second;
}

//生成编号
function getListNum(dateStr) {
    var random_str = "";
    var date_now = getTimeString(dateStr);
    var possible = "0123456789";
    for (var i = 0; i < 6; i++) {
        random_str += possible.charAt(Math.floor(Math.random() * possible.length));
    }
    return date_now + random_str;
}

var members = db.member_test.find({});

while(members.hasNext()) {
    //print(members.hasNext())
    var member = members.next();
   // print(member)
    var timeStr = getListNum(member.add_time);
    member.list_num = timeStr;
    member.member_img='img/user.png';
    member.guest_name='';
    member.system_id="MEMBER";
    member.custom_name='';
    member.member_sources= "会员系统";
    member.member_label=["会员系统"];
    member.isWxRegister=true;
    member.weixin_id='';
    member.nation='';
    member.wx_name='';
    member.wx_sex='';
    member.language='';
    member.area='';
    member.postcodes='';
    member.is_follow='';
    member.follow_time='';
    member.position='';
    member.company='';
    member.park_nums= [{"park_nums": ""}];
    member.papers=[]
    member.organization='59688d03798e5004d69dab47';
    member.isLevelsUpdate = "否";
    member.update_time ="";
    
    if(member.levels === '认证会员'){
        member.register_name=''
        member.id_num=member.paper_num
    }else{
        member.register_name=member.member_name
         member.id_num=''
    }
    if(member.levels === '普通会员'){
        member.id_name=''
        member.isAuthentication=false
    }else{
        member.id_name=member.member_name
        member.isAuthentication=true
    }
    member.create_time=member.create_card_time
    member.common_address=member.wx_register_address
    member.address=member.id_register_address
    member.papers.push({
        "paper_type":'身份证',
        "paper_num":member.id_num
     })
     
      member.member_name='';
      var reg = new RegExp( '/' , "g" );
     member.add_time=member.add_time.replace(reg , '-' );
     member.create_card_time=member.create_card_time.replace(reg , '-' );
     member.member_birth=member.member_birth.replace(reg , '-' );
     member.last_update_time=member.last_update_time.replace(reg , '-' );
     member.create_time=member.create_time.replace(reg , '-' );
     member.card_num=String(member.card_num*1000);
     member.member_phone=String(member.member_phone*1000);
     db.member_test.save(member);
    
   // print(timeStr);
  //  break;  
}
