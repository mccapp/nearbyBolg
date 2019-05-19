---
title: android 正则表达式
date: 2015-09-22 09:52:37
categories: android
tags: [android]
---

> android正则表达式及Pattern Matcher使用

    // 反斜杠
    /t 间隔 ('/u0009')
    /n 换行 ('/u000A')
    /r 回车 ('/u000D')
    /d 数字 等价于[0-9]
    /D 非数字 等价于[^0-9]
    /s 空白符号 [/t/n/x0B/f/r]
    /S 非空白符号 [^/t/n/x0B/f/r]
    /w 单独字符 [a-zA-Z_0-9]
    /W 非单独字符 [^a-zA-Z_0-9]
    /f 换页符
    /e Escape
    /b 一个单词的边界
    /B 一个非单词的边界
    /G 前一个匹配的结束

    ^为限制开头
    ^java     条件限制为以Java为开头字符
    $为限制结尾
    java$     条件限制为以java为结尾字符
    .  条件限制除/n以外任意一个单独字符
    java..     条件限制为java后除换行外任意两个字符


    加入特定限制条件「[]」
    [a-z]     条件限制在小写a to z范围中一个字符
    [A-Z]     条件限制在大写A to Z范围中一个字符
    [a-zA-Z] 条件限制在小写a to z或大写A to Z范围中一个字符
    [0-9]     条件限制在小写0 to 9范围中一个字符
    [0-9a-z] 条件限制在小写0 to 9或a to z范围中一个字符
    [0-9[a-z]] 条件限制在小写0 to 9或a to z范围中一个字符(交集)

    []中加入^后加再次限制条件「[^]」
    [^a-z]     条件限制在非小写a to z范围中一个字符
    [^A-Z]     条件限制在非大写A to Z范围中一个字符
    [^a-zA-Z] 条件限制在非小写a to z或大写A to Z范围中一个字符
    [^0-9]     条件限制在非小写0 to 9范围中一个字符
    [^0-9a-z] 条件限制在非小写0 to 9或a to z范围中一个字符
    [^0-9[a-z]] 条件限制在非小写0 to 9或a to z范围中一个字符(交集)

    在限制条件为特定字符出现0次以上时，可以使用「*」
    J*     0个以上J
    .*     0个以上任意字符
    J.*D     J与D之间0个以上任意字符

    在限制条件为特定字符出现1次以上时，可以使用「+」
    J+     1个以上J
    .+     1个以上任意字符
    J.+D     J与D之间1个以上任意字符

    在限制条件为特定字符出现有0或1次以上时，可以使用「?」JA? J或者JA出现

    限制为连续出现指定次数字符「{a}」
    J{2}     JJ
    J{3}     JJJ
    文字a个以上，并且「{a,}」
    J{3,}     JJJ,JJJJ,JJJJJ,???(3次以上J并存)
    文字个以上，b个以下「{a,b}」
    J{3,5}     JJJ或JJJJ或JJJJJ
    两者取一「|」
    J|A     J或A
    Java|Hello     Java或Hello

> 比如，在字符串包含验证时

    //查找以Java开头,任意结尾的字符串
    Pattern pattern  = Pattern.compile("^Java.*");
    Matcher matcher = pattern.matcher("Java棒棒的");
    boolean bool = matcher.matches();
    System.out.println(bool); //true
    
    //以多条件分割字符串时
    Pattern pattern = Pattern.compile("[,|]+");
    String[] strs = pattern.split("Java Hello World  Java,Hello,,World|Sun");
    for (int i=0;i<strs.length;i++) {  
     System.out.println(strs[i]);  
     //Java Hello World  Java
    //Hello
    //World
    //Sun
    }  
    
    //文字替换（首次出现字符）
    Pattern pattern = Pattern.compile("正则表达式");  
    Matcher matcher = pattern.matcher("正则表达式 Hello World,正则表达式 Hello       World");  
    //替换第一个符合正则的数据  
    System.out.println(matcher.replaceFirst("Java"));  
    //Java Hello World,正则表达式 Hello
    
    //文字替换（全部）
    Pattern pattern = Pattern.compile("正则表达式");  
    Matcher matcher = pattern.matcher("正则表达式 Hello World,正则表达式 Hello       World");  
    //替换第一个符合正则的数据  
    System.out.println(matcher.replaceAll("Java"));  
    //Java Hello World,Java Hello
    
    
    //◆文字替换（置换字符）
     Pattern pattern = Pattern.compile("正则表达式");
     Matcher matcher = pattern.matcher("正则表达式 Hello World,正则表达式 Hello World ");
     StringBuffer sbr = new StringBuffer();
     while (matcher.find()) {
            matcher.appendReplacement(sbr, "Java");
     }
     matcher.appendTail(sbr);
     System.out.println(sbr.toString());
     //Java Hello World,Java Hello World 
     
     
     //验证是否为邮箱地址
     String str="ceponline@yahoo.com.cn";  
     Pattern pattern =  
     Pattern.compile("[//w//.//-]+@([//w//-]+//.)+[//w//-]+",Pattern.CASE_INSENSI
     TIVE);  
     Matcher matcher = pattern.matcher(str);
     System.out.println(matcher.matches());  //true
     
    //去除html标记
     Pattern pattern = Pattern.compile("<.+?>", Pattern.DOTALL);  
     Matcher matcher = pattern.matcher("<a href=/"index.html/">主页</a>");  
     String string = matcher.replaceAll("");  
     System.out.println(string); //主页
     
    //查找html中对应条件字符串
    Pattern pattern = Pattern.compile("href=\"(.+?)\"");
    Matcher matcher = pattern.matcher("<a href=\"index.html\">主页</a>");
    if(matcher.find())
    System.out.println(matcher.group(1));//index.html
    
    //截取http://地址
    Pattern pattern = Pattern.compile("(http://|https://){1}[//w//.//-/:]+");  
    Matcher matcher = pattern.matcher("dsdsds<http://dsds//gfgffdfd>fdf");  
    StringBuffer buffer = new StringBuffer();  
    while(matcher.find()){                
    buffer.append(matcher.group());          
    buffer.append("/r/n");                
    System.out.println(buffer.toString());  
    }  
    
    //替换指定{}中文字
    String str = "Java目前的发展史是由{0}年-{1}年";  
    String[][] object={new String[]{"//{0//}","1995"},new 
    String[]{"//{1//}","2007"}};  
    System.out.println(replace(str,object));  
    public static String replace(final String sourceString,Object[] object) {  
            String temp=sourceString;      
            for(int i=0;i<object.length;i++){  
                      String[] result=(String[])object[i];  
               Pattern    pattern = Pattern.compile(result[0]);  
               Matcher matcher = pattern.matcher(temp);  
               temp=matcher.replaceAll(result[1]);  
            }  
            return temp;  
            }  //Java目前的发展史是由1995年-2007年
    
    
    

