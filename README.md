# CodeAcademy_DataScienceIndependentProject2
%Which tracks appeared in the most playlists? how many playlist did they appear in?
SELECT playlist_track.TrackId, 
		tracks.name, 
		COUNT(*)
FROM playlist_track
JOIN tracks
		ON playlist_track.TrackId = tracks.TrackId
GROUP BY playlist_track.TrackId
ORDER BY 3 DESC;


%Which track generated the most revenue? 
SELECT invoice_items.TrackId, 
		tracks.name, 
		SUM(invoice_items.UnitPrice)
FROM invoice_items
JOIN tracks
	ON invoice_items.TrackId = tracks.TrackId
GROUP BY invoice_items.TrackId
ORDER BY 3 DESC;


%which album? 
WITH trackRev_query AS (
SELECT invoice_items.TrackId AS 'TrackId', 
		tracks.name, 
		tracks.AlbumId AS 'AlbumId',
		SUM(invoice_items.UnitPrice) AS 'trackRev'
FROM invoice_items
JOIN tracks
	ON invoice_items.TrackId = tracks.TrackId
GROUP BY invoice_items.TrackId
ORDER BY 4 DESC
)
SELECT albums.AlbumId,
		albums.Title,
		SUM(trackRev_query.trackRev)
FROM albums
JOIN trackRev_query
	ON albums.AlbumId = trackRev_query.AlbumId
GROUP BY albums.AlbumId
ORDER BY 3 DESC;


%which genre?
WITH trackRev_query AS (
SELECT invoice_items.TrackId AS 'TrackId', 
		tracks.name, 
		tracks.GenreId AS 'GenreId',
		SUM(invoice_items.UnitPrice) AS 'trackRev'
FROM invoice_items
JOIN tracks
	ON invoice_items.TrackId = tracks.TrackId
GROUP BY invoice_items.TrackId
ORDER BY 4 DESC
)
SELECT genres.GenreId,
		genres.name,
		ROUND(SUM(trackRev_query.trackRev),2)
FROM genres
JOIN trackRev_query
	ON genres.GenreId = trackRev_query.GenreId
GROUP BY genres.GenreId
ORDER BY 3 DESC;


%Which countries have the highest sales revenue? What percent of total revenue does each country make up?
WITH previous AS (
	SELECT SUM(Total) AS 'TotalRevenue'
		FROM invoices
)
SELECT invoices.BillingCountry, 
	ROUND(SUM(invoices.Total),2) AS 'TotalRevenueByCountry',
	ROUND(SUM(invoices.Total) / previous.TotalRevenue , 2) AS 'RevenuePrecentageByCountry'
FROM invoices
CROSS JOIN previous
GROUP BY invoices.BillingCountry
ORDER BY 2 DESC;


%How many customers did each employee support, what is the average revenue for each sale, and what is their total sale?
SELECT employees.EmployeeId, 
		employees.LastName,
		COUNT(*) AS 'Number of Customers Supported'
FROM employees
JOIN customers
		ON customers.SupportRepId = employees.EmployeeId
GROUP BY employees.EmployeeId
ORDER BY 3 DESC;

SELECT customers.SupportRepId AS 'Employee ID',
		ROUND(AVG(invoices.Total),2) AS 'Average Sale by Employee',
		ROUND(SUM(invoices.Total),2) AS 'Total Sales by Employee'
FROM customers
JOIN invoices
		ON customers.CustomerId = invoices.CustomerId
GROUP BY 1;
