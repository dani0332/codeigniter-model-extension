Codeigniter Model Extension
---------------------------

The main purpose of these files is to make queries to database tables easier and create pagination with simple calls.

For Extending model classes you need put two files 
**MY_Model.php** 
**Pagination.php** 
in **application/core/**

Whenever you create model class, extend MY_Model instead of CI_Model like this

```php
    class Users extends MY_Model {
    
    }
```

Now, when you load this model in any controller, you can use like this

    class Base extends CI_Controller {
          function __construct() {
    		parent::__construct();	
    		$this->load->model('users');
    	}

		public function home() {
		 $data['users] = $this->users->getAll();
		}
    }

As you can see, we are calling getAll() method which will get all the rows from database table **users**.

If your database table name is different than model class name then you can add table name in protected property of your model class like this
		
     class Users extends MY_Model {
    	 protected $table  = 'app_users';    
      }

Now, all methods called using this model will load data from table **app_users** instead of **users**

Just like we are using **getAll()** method to get all rows from table users, you can also use below methods


To get count of all rows

    $this->users->count_all();

To get the rows with conditions passed to it

    $conditions = array('active' => 1);
    $this->users->getWhere($conditions);

Get single row with particular ID or Conditions

    $this->users->find(1);
    // or
    $conditions = array('id' => 1, 'active' => 1);
    $this->users->find($conditions);

To insert single row in table

    $row = array('name' => 'some name', 'active' => 1);
    $this->users->insert($row);
To insert multiple rows in table

    $rows = array(
          array('name' => 'some name', 'active' => 1),
          array('name' => 'some name 2', 'active' => 0)
          );
    $this->users->insertBatch($rows);

To update single row in table

  
    $id  = 1;
    $row = array('name' => 'some name', 'active' => 1);
    $this->users->update($id, $row);
To delete row in table

  
    $id  = 1;
    $this->users->delete($id);

To delete multiple rows in table

    $conditions = array('active' => 0);
    $this->users->deleteBatch($conditions);

To check value already exist of particular column

  
    $email = 'some@mail.com';
    $this->users->checkUnique('email', $email);

If you want to check value exist except for particular id of the table, you can pass 3rd parameter as id which you want to skip while checking value like this

    $email = 'some@mail.com';
    $id    = 2;
    $this->users->checkUnique('email', $email, $id);

Now, while checking for unique value it will skip the row with this id.

**Note:** Wherever the functions checking for ID will look for column **id** of that particular table not any other column name like **user_id** or **userid** or something else...



If you want to get paginated result of this same table with limit 10, you can use function like this

    $this->users->paginate(10);

If you want to get paginated result with some conditions, you can do by couple of ways

1) You can use codeigniter predefined function along with pagination method something like this

    $this->users->where('active', 1)->order_by('id', 'DESC')->paginate(10);

2) You can pass callback function as a parameter with conditions in it. Even here you can use predefined functions to extend the query.  Make sure you use parameter as query and return it.

	$conditions = function($query) {
		return $query->where('active', 1)->order_by('id', 'DESC');
	}
    $this->users->conditions($conditions)->paginate(10);

3) For changing the pagination config you can pass config as 3rd parameter to the method paginate. 2nd parameter is page no. to use while getting pagination through Ajax but for now you can pass it as null value.

    $config['uri_segment'] = 3;
    $config['base_url']    = 'index.php/users';
    $this->users->paginate(10, '', $config);


