## Links ##

 - [http://www.plusconsulting.com/blog/2013/05/crud-on-list-items-using-rest-services-jquery/](http://www.plusconsulting.com/blog/2013/05/crud-on-list-items-using-rest-services-jquery/)

## urls ##

 - [http://mttsrv32:107/SitePages/inicial.aspx](http://mttsrv32:107/SitePages/inicial.aspx)
 - [http://mttsrv32:107/_api/web/lists/](http://mttsrv32:107/_api/web/lists/)
 - [http://mttsrv32:107/_api/web/lists/LinksList/Items](http://mttsrv32:107/_api/web/lists/LinksList/Items)
 - [http://mttsrv32:107/_layouts/15/listfeed.aspx?List=%7B4423B4D7-2242-4C85-AE68-EC1AA85F7463%7D](http://mttsrv32:107/_layouts/15/listfeed.aspx?List=%7B4423B4D7-2242-4C85-AE68-EC1AA85F7463%7D)  ( Links Uteis )
 - [http://mttsrv32:107/_layouts/15/listfeed.aspx?List=%7BD89D9A7A-949C-42A2-9D7E-E3EE4622CDB9%7D](http://mttsrv32:107/_layouts/15/listfeed.aspx?List=%7BD89D9A7A-949C-42A2-9D7E-E3EE4622CDB9%7D)  ( Noticias )
 - [http://paulryan.com.au/2014/spo-remote-authentication-rest/] Autenticação
 - [https://blogs.msdn.microsoft.com/richard_dizeregas_blog/2014/06/27/displaying-cross-domainsecure-images-from-sharepoint-apps/]
 - [https://msdn.microsoft.com/en-us/magazine/dn198245.aspx]

## funcao para pegar o conteudo de uma lista em json ##


    function getListItem(url, listname) {
    	// Getting our list items
    	$j.ajax({
    		url: url + "/_api/web/lists/" + listname +"/items",
    		method: "GET",
    		headers: { "Accept": "application/json; odata=verbose" },
    		success: function (data) {
    			// Returning the results
    			console.log(data);
    		},
    		error: function (data) {
    			console.log(data);
    		}
    	});
    }


