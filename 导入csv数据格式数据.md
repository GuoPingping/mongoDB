1. Excel数据格式处理
    对于多个sheet列，需要合并为一列
```  
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
``` 

```    
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
```

2. excel数据格式转csv数据格式
    选择excel文件，点击另存为csv数据格式

3. mongoDB执行命令
    找到mongoDB安装目录，进入bin所在文件夹，执行：
    mongoimport.exe -d 数据库名 -c 表名 --type csv --headerline --ignoreBlanks --file 导入文件所在的路径

 注：中文乱码，可以选择用editPlus打开csv软件，更改编码方式为utf-8，保存更改，再进行导入即可。

//对于一些无法处理或是有条件限制的字段，可以编写脚本执行
    ```
    function formatNumber(nubmer) {
        return nubmer < 10 ? '0' + nubmer : nubmer;
    }

    function getTimeString(dateStr,fmt){
        date = new Date(dateStr);
        var o = {
            "M+": date.getMonth() + 1, //月份 
            "d+": date.getDate(), //日 
            "h+": date.getHours(), //小时 
            "m+": date.getMinutes(), //分 
            "s+": date.getSeconds(), //秒 
            "q+": Math.floor((date.getMonth() + 3) / 3), //季度 
            "S": date.getMilliseconds() //毫秒 
        };
        if (/(y+)/.test(fmt)) fmt = fmt.replace(RegExp.$1, (date.getFullYear() + "").substr(4 - RegExp.$1.length));
        for (var k in o)
            if (new RegExp("(" + k + ")").test(fmt)) fmt = fmt.replace(RegExp.$1, (RegExp.$1.length == 1) ? (o[k]) : (("00" + o[k]).substr(("" + o[k]).length)));
        return fmt;
    }

    //生成编号
    function getListNum(dateStr) {
        var random_str = "";
        var date_now =getTimeString(dateStr,"yyMMddhhmmssSS");
        var possible = "0123456789";
        for (var i = 0; i < 4; i++) {
            random_str += possible.charAt(Math.floor(Math.random() * possible.length));
        }
        return date_now + random_str;
    }

    var members = db.member_test.find({});

    while(members.hasNext()) {
        var member = members.next();
        var timeStr = getListNum(new Date(member.add_time));
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
            member.id_num=(member.paper_num+'').toString()
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
        
        member.common_address=member.wx_register_address
        member.address=member.id_register_address
        member.papers.push({
            "paper_type":'身份证',
            "paper_num":(member.id_num+'').toString()
        })
        
        member.member_name='';
        member.add_time=getTimeString(new Date(member.add_time),"yyyy-MM-dd hh:mm:ss")
        member.create_card_time=getTimeString(new Date(member.create_card_time),"yyyy-MM-dd hh:mm:ss")
        member.last_update_time=getTimeString(new Date(member.last_update_time),"yyyy-MM-dd hh:mm:ss")
        member.create_time=member.create_card_time
        if(member.member_birth!==''&&member.member_birth!==undefined){
            member.member_birth=getTimeString(new Date(member.member_birth),"yyyy-MM-dd")
        }
        member.card_num=(member.card_num+'').toString()
        member.member_phone=(member.member_phone+'').toString()
        member.paper_num=(member.paper_num+'').toString()
        db.member_test.save(member);
    }

```