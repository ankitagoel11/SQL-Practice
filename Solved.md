## SQL Practice Using the sakila database ( comes with mysql installation)

* 1a. You need a list of all the actors who have Display the first and last names of all actors from the table `actor`.
Solution: `SELECT DISTINCT first_name, last_name
FROM sakila.actor;`

* 1b. Display the first and last name of each actor in a single column in upper case letters. Name the column `Actor Name`. 

Solution: `SELECT *, CONCAT(first_name, ' ', last_name) AS Actor_Name FROM sakila.actor;`

* 2a. You need to find the ID number, first name, and last name of an actor, of whom you know only the first name, "Joe." What is one query would you use to obtain this information? 

Solution: `SELECT actor_id, first_name, last_name FROM sakila.actor WHERE first_name LIKE "Joe";`
  	
* 2b. Find all actors whose last name contain the letters `GEN`:
Solution: `SELECT * FROM sakila.actor WHERE last_name LIKE "%GEN%";`
  	
* 2c. Find all actors whose last names contain the letters `LI`. This time, order the rows by last name and first name, in that order: 
Solution: `SELECT last_name, first_name FROM sakila.actor WHERE last_name LIKE "%LI%";`

* 2d. Using `IN`, display the `country_id` and `country` columns of the following countries: Afghanistan, Bangladesh, and China:
Solution: `SELECT country_id, country FROM sakila.country
WHERE country IN ('Afghanistan', 'Bangladesh', 'China');`

* 3a. Add a `middle_name` column to the table `actor`. Position it between `first_name` and `last_name`. Hint: you will need to specify the data type.
Solution: `ALTER table sakila.actor Add column middle_name CHAR(20) AFTER first_name;`
  	
* 3b. You realize that some of these actors have tremendously long last names. Change the data type of the `middle_name` column to `blobs`. 
Solution: `ALTER TABLE sakila.actor MODIFY COLUMN middle_name blob # there is nothing as blobs.`

* 3c. Now delete the `middle_name` column.
Solution: `ALTER table actor DROP middle_name;`

* 4a. List the last names of actors, as well as how many actors have that last name.
Solution: `SELECT last_name, COUNT(last_name)  FROM sakila.actor GROUP BY last_name;`
  	
* 4b. List last names of actors and the number of actors who have that last name, but only for names that are shared by at least two actors
Solution: `SELECT last_name, COUNT(last_name)  FROM sakila.actor GROUP BY last_name HAVING COUNT(last_name) > 1;`


* 4c. Oh, no! The actor `HARPO WILLIAMS` was accidentally entered in the `actor` table as `GROUCHO WILLIAMS`, the name of Harpo's second cousin's husband's yoga teacher. Write a query to fix the record.

Solution: `Update sakila.actor SET first_name = 'HARPO' WHERE last_name LIKE "WILLIAMS" AND first_name like "GROUCHO";`
  	
* ###4d. Perhaps we were too hasty in changing `GROUCHO` to `HARPO`. It turns out that `GROUCHO` was the correct name after all! In a single query, if the first name of the actor is currently `HARPO`, change it to `GROUCHO`. Otherwise, change the first name to `MUCHO GROUCHO`, as that is exactly what the actor will be with the grievous error. BE CAREFUL NOT TO CHANGE THE FIRST NAME OF EVERY ACTOR TO `MUCHO GROUCHO`, HOWEVER! (Hint: update the record using a unique identifier.) # VERY CONFUSING

* 5a. You cannot locate the schema of the `address` table. Which query would you use to re-create it?
Solution: `desc sakila.address;`

* 6a. Use `JOIN` to display the first and last names, as well as the address, of each staff member. Use the tables `staff` and `address`:

Solution: `SELECT s.first_name, s.last_name, a.address 
FROM sakila.staff s
INNER JOIN sakila.address a
ON a.address_id = s.address_id;`

* 6b. Use `JOIN` to display the total amount rung up by each staff member in August of 2005. Use tables `staff` and `payment`. 

Solution: `SELECT SUM(p.amount) AS Amount_in_August_2005, p.staff_id 
FROM sakila.payment p
INNER JOIN sakila.staff s
ON p.staff_id = s.staff_id
where p.payment_date LIKE "2005-08%" 
GROUP BY staff_id;`

  	
* 6c. List each film and the number of actors who are listed for that film. Use tables `film_actor` and `film`. Use inner join.

Solution: `SELECT f.title, count(fa.actor_id) AS Number_of_actors
FROM sakila.film f
INNER JOIN sakila.film_actor fa
ON f.film_id = fa.film_id
GROUP BY f.title;`


* 6d. How many copies of the film `Hunchback Impossible` exist in the inventory system?

Solution: `SELECT COUNT(inventory_id) AS Count_of_Hunchback_Impossible FROM sakila.inventory 
WHERE film_id = (SELECT film_id FROM sakila.film WHERE title LIKE "HUNCHBACK IMPOSSIBLE");`


* 6e. Using the tables `payment` and `customer` and the `JOIN` command, list the total paid by each customer. List the customers alphabetically by last name:
SOLUTION: 
`SELECT c.first_name, c.last_name, SUM(p.amount) AS Total_Amount_Paid
FROM sakila.payment p
INNER JOIN sakila.customer c
ON c.customer_id = p.customer_id
GROUP BY c.last_name
ORDER BY c.last_name;`



  ```
  	![Total amount paid](Images/total_payment.png)
  ```

* 7a. The music of Queen and Kris Kristofferson have seen an unlikely resurgence. As an unintended consequence, films starting with the letters `K` and `Q` have also soared in popularity. Use subqueries to display the titles of movies starting with the letters `K` and `Q` whose language is English. 



* 7b. Use subqueries to display all actors who appear in the film `Alone Trip`.

Solution: SELECT a.first_name, a.last_name FROM actor a
WHERE actor_id IN (SELECT actor_id FROM film_actor 
WHERE film_id = (SELECT f.film_id FROM sakila.film f WHERE title = "ALONE TRIP"));
   
* 7c. You want to run an email marketing campaign in Canada, for which you will need the names and email addresses of all Canadian customers. Use joins to retrieve this information.

SOLUTION: `SELECT c.first_name, c.last_name, c.email
FROM customer c
INNER JOIN address a ON a.address_id = c.address_id
INNER JOIN city    ci ON ci.city_id   = a.city_id
INNER JOIN country co ON co.country_id   = ci.country_id
WHERE co.country_id = (SELECT country_id from country where country = "Canada" );`

* 7d. Sales have been lagging among young families, and you wish to target all family movies for a promotion. Identify all movies categorized as family films.

Solution: `SELECT title as Family_Movie_Names FROM sakila.film
WHERE film_id IN (SELECT film_id FROM sakila.film_category 
WHERE category_id = (SELECT category_id FROM sakila.category WHERE name = "Family"));`

* 7e. Display the most frequently rented movies in descending order.
Solution:
`SELECT count(r.inventory_id) AS Number_of_times_Rented, f.title
FROM sakila.film f
INNER JOIN sakila.inventory i
ON i.film_id = f.film_id
INNER JOIN sakila.rental r
ON r.inventory_id = i.inventory_id
GROUP BY f.title
ORDER BY COUNT(r.inventory_id) DESC;`


  	
* 7f. Write a query to display how much business, in dollars, each store brought in.
Solution:
`SELECT s.store_id, SUM(p.amount) AS Total_Amount
FROM sakila.payment p
INNER JOIN sakila.staff st
ON st.staff_id = p.staff_id
INNER JOIN sakila.store s
ON st.store_id = s.store_id
GROUP BY s.store_id;`


* 7g. Write a query to display for each store its store ID, city, and country.

solution:
`SELECT s.store_id, c.city, co.country
FROM sakila.store s
INNER JOIN sakila.address a
ON s.address_id = a.address_id
INNER JOIN sakila.city c
ON c.city_id = a.city_id
INNER JOIN sakila.country co
ON co.country_id = c.country_id
GROUP BY s.store_id;`

  	
* 7h. List the top five genres in gross revenue in descending order. (**Hint**: you may need to use the following tables: category, film_category, inventory, payment, and rental.)
  	
* 8a. In your new role as an executive, you would like to have an easy way of viewing the Top five genres by gross revenue. Use the solution from the problem above to create a view. If you haven't solved 7h, you can substitute another query to create a view.
  	
* 8b. How would you display the view that you created in 8a?

* 8c. You find that you no longer need the view `top_five_genres`. Write a query to delete it.

### Appendix: List of Tables in the Sakila DB

* A schema is also available as `sakila_schema.svg`. Open it with a browser to view.

```sql
	'actor'
	'actor_info'
	'address'
	'category'
	'city'
	'country'
	'customer'
	'customer_list'
	'film'
	'film_actor'
	'film_category'
	'film_list'
	'film_text'
	'inventory'
	'language'
	'nicer_but_slower_film_list'
	'payment'
	'rental'
	'sales_by_film_category'
	'sales_by_store'
	'staff'
	'staff_list'
	'store'
```
