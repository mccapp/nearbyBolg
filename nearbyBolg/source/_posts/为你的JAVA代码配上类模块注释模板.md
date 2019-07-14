---
title: 为你的JAVA代码配上类模块注释模板
date: 2016-12-05 13:30:12
categories: android studio
tags: [android studio]
---


模板1：


/*******************************************************************************************
 * 类描述：
 *
 * @author: ${user}
 * @date： ${date} ${time}
 * ${tags}
 * @version 1.0
 *
 *
 * Version    Date       ModifiedBy                 Content
 * -------- ---------    ----------         ------------------------
 * 1.0      ${date}       ${user}                             
 *******************************************************************************************
 */

--------------------- 

模板1效果：

/**
 * *****************************************************************************************
 * 类描述：
 *
 * @author: 逍遥梦
 * @date： 2014-2-22 上午10:38:41
 * @version 1.0
 *
 *
 * Version    Date                ModifiedBy                 Content
 * --------        ---------               ----------                     -----------------------
 * 1.0             2014-2-22         逍遥梦                             
 *******************************************************************************************
 */

--------------------- 

模板2（个人比较喜欢）：

/**   
 * Simple to Introduction  
 * @ProjectName:  [${project_name}] 
 * @Package:      [${package_name}]  
 * @ClassName:    [${type_name}]   
 * @Description:  [一句话描述该类的功能]   
 * @Author:       [${user}]   
 * @CreateDate:   [${date} ${time}]   
 * @UpdateUser:   [${user}]   
 * @UpdateDate:   [${date} ${time}]   
 * @UpdateRemark: [说明本次修改内容]  
 * @Version:      [v1.0] 
 *    
 */

--------------------- 

模板2效果：
/**
 * 
 * 
 * Simple to Introduction  
 * @ProjectName:  [EmpMis] 
 * @Package:      [cn.xym.empmis.service.impl]  
 * @ClassName:    [BussinessServiceImpl]   
 * @Description:  [一句话描述该类的功能]   
 * @Author:       [Administrator]   
 * @CreateDate:   [2014-2-22 上午10:56:16]   
 * @UpdateUser:   [Administrator]   
 * @UpdateDate:   [2014-2-22 上午10:56:16]   
 * @UpdateRemark: [说明本次修改内容]  
 * @Version:      [v1.0] 
 *
 */

--------------------- 


模板3：


 /**   
  * 
  * Simple To Introduction
  * 项目名称:  [${project_name}]
  * 包:        [${package_name}]    
  * 类名称:    [${type_name}]  
  * 类描述:    [一句话描述该类的功能]
  * 创建人:    [${user}]   
  * 创建时间:  [${date} ${time}]   
  * 修改人:    [${user}]   
  * 修改时间:  [${date} ${time}]   
  * 修改备注:  [说明本次修改内容]  
  * 版本:      [v1.0]   
  *    
  */

--------------------- 

模板3效果：

/**
  * 
  * Simple To Introduction
  * 项目名称:  [EmpMis]
  * 包:       [cn.xym.empmis.service.impl]    
  * 类名称:    [BussinessServiceImpl]  
  * 类描述:    [一句话描述该类的功能]
  * 创建人:    [Administrator]   
  * 创建时间:  [2014-2-22 上午10:58:49]   
  * 修改人:    [Administrator]   
  * 修改时间:  [2014-2-22 上午10:58:49]   
  * 修改备注:  [说明本次修改内容]  
  * 版本:     [v1.0]   
  *
  */

--------------------- 

