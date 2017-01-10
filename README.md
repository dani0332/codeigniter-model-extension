Codeigniter Model Extension
---------------------------

The main purpose of these files is to make queries to database tables easier and create pagination with simple calls.

For extending model classes you need to put two files 

 1. **MY_Model.php**
 2. **Pagination.php** 
    
in **application/core/**

Whenever you create model class, extend MY_Model instead of CI_Model like this

```php
    class Users extends MY_Model {
    
    }
```

Now, when you load this model in any controller, you can use like this

```php
    class Base extends CI_Controller {
          function __construct() {
    		parent::__construct();	
    		$this->load->model('users');
    	}

		public function home() {
		 $data['users'] = $this->users->getAll();
		}
    }
```

As you can see, we are calling getAll() method which will get all the rows from database table **users**.

If your database table name is different than model class name then you can add table name in protected property of your model class like this

```php		
     class Users extends MY_Model {
    	 protected $table  = 'app_users';    
      }
```

Now, all methods called using this model will load data from table **app_users** instead of **users**

Just like we are using **getAll()** method to get all rows from table users, you can also use below methods


To get count of all rows

```php
    $this->users->count_all();
```

To get the rows with conditions passed to it

```php
    $conditions = array('active' => 1);
    $this->users->getWhere($conditions);
```

Get single row with particular ID or Conditions

```php
    $this->users->find(1);
    // or
    $conditions = array('id' => 1, 'active' => 1);
    $this->users->find($conditions);
```

Alternatively, you can pass 2nd parameter as column name if you don't want to check with column **id**

```php
    $this->users->find(1, 'userid');
```

For getting maximum, minimum, average and sum of values, you can use these below methods. The only parameter used here is the name of table column name

```php
    $this->users->max('points');
    $this->users->min('points');
    $this->users->avg('points');
    $this->users->sum('points');
```

To insert single row in table

```php
    $row = array('name' => 'some name', 'active' => 1);
    $this->users->insert($row);
```

To insert multiple rows in table

```php
    $rows = array(
          array('name' => 'some name', 'active' => 1),
          array('name' => 'some name 2', 'active' => 0)
          );
    $this->users->insertBatch($rows);
```

To update single row in table

```php  
    $id  = 1;
    $row = array('name' => 'some name', 'active' => 1);
    $this->users->update($id, $row);
```

To delete row in table

```php  
    $id  = 1;
    $this->users->delete($id);
```

To delete multiple rows in table

```php
    $conditions = array('active' => 0);
    $this->users->deleteBatch($conditions);
```

To check value already exist of particular column

```php  
    $email = 'some@mail.com';
    $this->users->checkUnique('email', $email);
```

If you want to check value exist except for particular id of the table, you can pass 3rd parameter as id which you want to skip while checking value like this

```php
    $email = 'some@mail.com';
    $id    = 2;
    $this->users->checkUnique('email', $email, $id);
```

Now, while checking for unique value it will skip the row with this id.

**Note:** Wherever the functions checking for ID will look for column **id** of that particular table not any other column name like **user_id** or **userid** or something else...


## Pagination

If you want to get paginated result of this same table with limit 10, you can use function like this

```php
    $this->users->paginate(10);
```

If you want to get paginated result with some conditions, you can do by couple of ways

1) You can use codeigniter predefined function along with pagination method something like this

```php
    $this->users->where('active', 1)->order_by('id', 'DESC')->paginate(10);
```

2) You can pass callback function as a parameter with conditions in it. Even here you can use predefined functions to extend the query.  Make sure you use parameter as query and return it.

```php
	$conditions = function($query) {
		return $query->where('active', 1)->order_by('id', 'DESC');
	}
    $this->users->conditions($conditions)->paginate(10);
```

3) For changing the pagination config you can pass config as 3rd parameter to the method paginate. 2nd parameter is page no. to use while getting pagination through Ajax but for now you can pass it as null value.

```php
    $config['uri_segment'] = 3;
    $config['base_url']    = 'index.php/users';

    $users   = $this->users->paginate(10, '', $config);
    $links   = $this->users->getLinks();
```
**getLinks()** method will get pagination links with some default settings. Pass custom config settings to get the desired results for both pagination result and links. You can also check the constructor method of **Pagination.php** to see what default settings it is using.

**Note:** For exisitng and custom config of pagination, do not forget to create respective routes in routes.php


## Using Pre defined methods

You can use all the pre defined db methods of codeigniter as a chain as well to get the result. Below are the examples

```php
 $this->users->order_by('id', 'DESC')->get()->result();
 $this->users->order_by('id', 'DESC')->get()->result_array();
 $this->users->order_by('id', 'DESC')->get()->row();
 $this->users->where('id', 1)->get()->row();
```

You can also skip get() method and fetch the results. As get() method will automatically be called based on functions.

```php
 $this->users->order_by('id', 'DESC')->result();
 $this->users->order_by('id', 'DESC')->result_array();
 $this->users->order_by('id', 'DESC')->row();
 $this->users->where('id', 1)->row();
```


## Hidden column values

We can make the results set to hide the column values from rows. For example, If we have a table **users** and we don't want to allow **password** and **token** to be available whenever user information is fetched. We can define protected array variable with name **hidden** in model class like this

```php		
     class Users extends MY_Model {
    	 protected $hidden = array('password', 'token');   
      }
```

**Note:** You can achieve this only by calling methods from the model object not by using **$this->db** separately somehwere

## Fillable columns

This option will not let any query to add any column data we pass for saving or updating column values of table. Let's say, We want users to allow only **email**, **name** and **password** to be inserted or updated and rest of the column values must be ignored even if they pass it. We can define protected array variable with name **fillable** in model class like this

```php		
     class Users extends MY_Model {
    	 protected $fillable = array('email', 'name', 'password');   
      }
```

**Note:** You can achieve this only by calling methods from the model object not by using **$this->db** separately somehwere

## Relations

For getting rows or single column value from other database tables based on relations, you can define one protected function in your model class with name **setRelations**

```php		
     class Users extends MY_Model {
    	 protected function setRelations() {}
      }
```

There are three ways, we can attach data to our results

1. Attaching multiple rows

Let's say, we have **books** table which is having foriegn key **user_id** of **users** table. Now for getting number of books belongs to users along with users result data, we can add the relation like this

```php
	protected function setRelations() {
		$this->addRelation(array(
                    'primary'   => 'id',      
		    'table'     => 'books', 
		    'foreign'   => 'user_id',
		    'variable'  => 'books',
		  ));
	}
```

### These are the options we can pass
a) **primary** 	- Name of the primary key of table. If not passed, it will take **id** as default
b) **table**   	- Name of the table from which rows needs to fetched.
c) **model**   	- Instead of table you can put **model** name which is created the way it is mentioned above.
d) **variable**   	- Name of the variable that needs to be added in each row of result set. If not passed, it will take foreign table name in lower case if mentioned.




2. Attaching single column value

3. Attaching single column value with some modification



