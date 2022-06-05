---
id: 133
title: 'ADO.NET实体框架连接串引发的异常：Unable to load the specified metadata resource'
date: '2009-09-09T10:46:09+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=133'
permalink: /2009/09/unable-to-load-the-specified-metadata-resource
categories:
    - 'net&amp;c#'
---

在项目中使用Unity作为IOC串联整个项目，整合WCF，ADO.NET Entity Framework，最初表现为Unity无法构造对象，逐步排除后发现是在使用ADO.NET Entity Framework构建Application时出现如下异常：Unable to load the specified metadata resource。  
异常信息：  
System.Data.MetadataException was unhandled  
Message=”Unable to load the specified metadata resource.”  
Source=”System.Data.Entity”  
StackTrace:  
 at System.Data.Metadata.Edm.MetadataArtifactLoaderCompositeResource.LoadResources(String assemblyName, String resourceName, ICollection`1 uriRegistry, MetadataArtifactAssemblyResolver resolver)  
 at System.Data.Metadata.Edm.MetadataArtifactLoaderCompositeResource.CreateResourceLoader(String path, ExtensionCheck extensionCheck, String validExtension, ICollection`1 uriRegistry, MetadataArtifactAssemblyResolver resolver)  
 at System.Data.Metadata.Edm.MetadataArtifactLoader.Create(String path, ExtensionCheck extensionCheck, String validExtension, ICollection`1 uriRegistry, MetadataArtifactAssemblyResolver resolver)  
 at System.Data.EntityClient.EntityConnection.SplitPaths(String paths)  
 at System.Data.EntityClient.EntityConnection.GetMetadataWorkspace(Boolean initializeAllCollections)  
 at System.Data.EntityClient.EntityConnection.InitializeMetadata(DbConnection newConnection, DbConnection originalConnection, Boolean closeOriginalConnectionOnFailure)  
 at System.Data.EntityClient.EntityConnection.Open()  
抛出异常Exception – Unable to load the specified metadata resource。经检查，问题出现在App.config 配置文件（该配置文件在使用ADO.NET Entity Data Model向导时自动添加），移动EDM文件的位置的时候会去修改app.config中的连接串信息。所以在移动EDM文件的时候要记得更新数据库连接串信息。