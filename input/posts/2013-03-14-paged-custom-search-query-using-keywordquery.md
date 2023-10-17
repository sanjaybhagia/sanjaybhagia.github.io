---
id: 188
title: Paged Custom Search Query using KeywordQuery
date: 2013-03-14T09:16:17+02:00
author: sanjay.bhagia@gmail.com
layout: post
permalink: /2013/03/14/paged-custom-search-query-using-keywordquery/
disqus_identifier: https://www.sanjaybhagia.com/2013/03/14/paged-custom-search-query-using-keywordquery/
categories:
  - PowerShell
  - SharePoint
tags:
  - Custom Search
  - KeywordQuery
  - People Search
  - RelevantResults
  - ResultTable
  - Search
  - SharePoint Search
---
<p style="text-align:justify;">SharePoint Server Object Model provides two classes to perform custom search queries namely <a href="http://msdn.microsoft.com/en-us/library/microsoft.sharepoint.search.query.fulltextsqlquery(v=office.14).aspx">FullTextSqlQuery </a>and <a href="http://msdn.microsoft.com/en-us/library/microsoft.sharepoint.search.query.keywordquery(v=office.14).aspx">KeywordQuery</a>. Both of these classes inherit from a generic Query class (in  Microsoft.SharePoint.Search namespace).</p>
<p style="text-align:justify;">You can perform search operations using both of these classes depending on your needs as FullTextSqlQuery provides more sophisticated queries using Sql syntax whereas KeywordQuery class provides simpler syntax to do the search.</p>
<p style="text-align:justify;">However, the scenario I'm explaining in this post is relevant to both of these classes but i will use KeywordQuery class to demonstrate.</p>
<p style="text-align:justify;">Scenario:</p>
<p style="text-align:justify;">I want to write a custom people search web part that takes in the keywords entered by user and performs the search.</p>
<p style="text-align:justify;"><em>I'm using powershell to demonstrate here but this can be done in C# as well in similar fashion.</em></p>
<p style="text-align:justify;">So, here is the function that performs the basic search</p>

<pre><code class="ps">
Function GetSearchResults([string]$siteCollectionUrl, [string]$keyword, [int]$sIndex, [int]$rLimit)
{
	#load the site and set the keyword query object
	$searchSite = Get-SPSite $siteCollectionUrl

	$keywordQuery = New-Object Microsoft.Office.Server.Search.Query.KeywordQuery $searchSite

	$keywordQuery.ResultTypes = [Microsoft.Office.Server.Search.Query.ResultType]::RelevantResults

  	#Write-Host &quot;Start Index &quot; $sIndex
	#Write-Host &quot;Row Limit &quot; $rLimit
	#Write-Host &quot;Keyword &quot; $keyword

	$keywordQuery.StartRow = $sIndex
	$keywordQuery.RowLimit = $rLimit
	$keywordQuery.HiddenConstraints = &quot;scope:People&quot;

	#Pass the query text - SharePoint
	$keywordQuery.QueryText = $keyword

	#query execution
	$results = $keywordQuery.Execute()
	return $results
}
</code></pre>
<p style="text-align:justify;">KeywordQuery class has two properties (RowLimit and StartRow) that are of our interest here.
<strong>RowLimit</strong> specifies the number of items that you want the search to return when you execute the query
and <strong>StartRow</strong> gets or sets the first row of information from the search results. So basically with both of these properties you get the paged search result instead of getting entire data in one chunk !</p>
<p style="text-align:justify;">Now, one of the obvious reasons to do this performance. But there is one more reason as well. RowLimit (<em>default value is 50 if you don't specify it yourself</em>), even though can accept maximum 32-bit integer value theoretically, is set to some hardcoded limit of 10000. It means that if you set RowLimit to anything greater that 10000 you will get an exception when you execute the query.</p>
<p style="text-align:justify;">The exception is something like this:</p>

<pre>"Exception from HRESULT: 0x80040E01"</pre>
Here is the StackTrace from ULS log:
<pre><code class="">Log Query: More Information: Row could not be inserted into the rowset without exceeding provider's maximum number of active rows.

SearchServiceApplication::Execute--Exception: System.Runtime.InteropServices.COMException (0x80040E01): Exception from HRESULT: 0x80040E01
at System.Runtime.InteropServices.Marshal.ThrowExceptionForHRInternal(Int32 errorCode, IntPtr errorInfo)
at Microsoft.Office.Server.Search.Query.KeywordQueryInternal.Execute()
at Microsoft.Office.Server.Search.Query.QueryInternal.Execute(QueryProperties properties)
at Microsoft.Office.Server.Search.Administration.SearchServiceApplication.Execute(QueryProperties properties)

SearchServiceApplicationProxy::Execute--Error occured: System.ServiceModel.FaultException`1[System.ServiceModel.ExceptionDetail]:
Exception from HRESULT: 0x80040E01 (Fault Detail is equal to An ExceptionDetail, likely created by IncludeExceptionDetailInFaults=true,
whose value is: System.Runtime.InteropServices.COMException: Exception from HRESULT: 0x80040E01
at System.Runtime.InteropServices.Marshal.ThrowExceptionForHRInternal(Int32 errorCode, IntPtr errorInfo)
at Microsoft.Office.Server.Search.Query.KeywordQueryInternal.Execute()
at Microsoft.Office.Server.Search.Query.QueryInternal.Execute(QueryProperties properties)
at Microsoft.Office.Server.Search.Administration.SearchServiceApplication.Execute(QueryProperties properties)
at SyncInvokeExecute(Object , Object[] , Object[] )
at System.ServiceModel.Dispatcher.SyncMethodInvoker.Invoke(Object instance, Object[] inputs, Object[]&amp; outputs)
at System.ServiceModel.Dispatcher.DispatchOperationRuntime.InvokeBegin(MessageRpc&amp; rpc)
at System.ServiceModel.Dispatcher.ImmutableDispatchRuntime.ProcessMessage5(MessageRpc&amp; rpc)
at System.ServiceModel.Dispatcher.ImmutableDispatchRuntime.ProcessMessage4(MessageRpc&amp; rpc) ...).</code></pre>
You can determine this limit by this method in your environment.

<pre><code class="ps"># Get the maximum results returned value set for KeywordQuery and FullTextQuery in current environment
Function GetMaxResultsReturned()
{
 # Get reference to Search Service Application
 $ssa = Get-SPEnterpriseSearchServiceApplication
 return $ssa.GetSetting('Config:qp_MaxResultsReturned')
}
</code></pre>
This value is configured at Search Service Application level. Now this could be different for your environment but default is 10000. So if you set RowLimit to anything greater than this value ('Config:qp_MaxResultsReturned') you will get an exception.
You can modify this value if you want using this function

<pre><code class="ps"># Sets the maximum results returned value set for KeywordQuery and FullTextQuery in current environment
Function SetMaxResultsReturned($newLimit)
{
	$ssa = Get-SPEnterpriseSearchServiceApplication
	$ssa.UpdateSetting('Config:qp_MaxResultsReturned', $newLimit)
	$ssa.Update()
}
</code></pre>
<p style="text-align:justify;">So, in order to avoid this exception, you should query to retrieve results in chunks and then merge them. In this way you don't loose on performance and also you will get your results back without facing this exception.</p>
<p style="text-align:justify;">Following, I'm calling the GetSearchResults function (defined above) to perform people search query by retrieving results in pages and merging them in a DataTable.</p>

<pre><code class="ps">
$startIndex = 0
$rowLimit = 50
$siteCollectionUrl = &quot;&lt;Url of the site collection&gt;&quot;
$searchKey = &quot;s*&quot;

$resultCollection = GetSearchResults $siteCollectionUrl $searchKey $startIndex $rowLimit
$hits = $resultCollection.TotalRows
Write-Host Total hits: $hits
$resultsDataTable = $resultCollection.Table

#Iterate through the rest of the pages to fetch items
while($resultCollection.TotalRows -gt $resultsDataTable.Rows.Count)
{
	$sIndex = $resultsDataTable.Rows.Count
	$resultCollection = GetSearchResults $searchKey $startIndex $rowLimit
        $resultsDataTable.Load($resultCollection, [System.Data.LoadOption]::PreserveChanges)
}

# display the results in table format for better view
$resultsDataTable | Format-Table -AutoSize -Property title , url
</code></pre>

Hope it helps!