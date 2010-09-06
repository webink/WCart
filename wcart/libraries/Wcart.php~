<?php defined('SYSPATH') OR die('No direct access allowed.');
/**
 * @package    Wcart
 * @author     Webink.it snc 
 * @license    GPL
 */

class Wcart_Core {
	
	protected $config = array();
	protected $items = array();

	/**
	 * Creates a new Wcart instance and returns it.
	 * @chainable
	 * @param   array config params: min_size, max_size, shuffle, view
	 * @return  Wcart
	 */
	public static function factory($config=array())
	{
		return new Wcart($config);
	}
	
	/**
	 * Load by an array of item_id => qty
	 * @param object $items
	 * @return 
	 */
	public function load($items){
		$prod_id_col = $this->config['prod_id'];
		foreach($items as $key => $value){
			$prod = new $this->config['model_name']();
			$prod = $prod->where($this->config['prod_id'], $key)->find();
			if($prod->id != ''){
				$this->add_item($prod, $value);
			}
		}
	}
	
	
	/**
	 * Constructor
	 * @param   array config params: model_name, prod_id, name, price
	 */
	public function __construct($config=array())
	{
		//load default configuration
		$this->config=Kohana::config_load('wcart');
		$this->config=array_merge($this->config,$config);
	}
	
	/**
	 * Add an item to the cart
	 * @param object $item
	 * @param object $qty [optional]
	 * @return 
	 */
	public function add_item($item, $qty = 1){
		$prod_id_col = $this->config['prod_id'];
		if(array_key_exists($item->$prod_id_col, $this->items)){
			$this->items[$item->$prod_id_col]['qty'] += $qty;
		}
		else{
			$this->items[$item->$prod_id_col]['object'] = $item;
			$this->items[$item->$prod_id_col]['qty'] = $qty;
		}
	}
	/**
	 * Update quantity of an item
	 * @param object $item
	 * @param object $qty
	 * @return 
	 */
	public function update_item($item, $qty){
		$prod_id_col = $this->config['prod_id'];
		if(array_key_exists($item->$prod_id_col, $this->items)){
			if($qty == 0){
				unset($this->items[$item->$prod_id_col]);
			}
			else if($qty > 0){
				$this->items[$item->$prod_id_col]['qty'] = $qty;
			}
		}
	}
	
	/**
	 * Reset cart
	 * @return 
	 */
	public function reset(){
		$this->items = array();
		$this->del_session();
	}
	
	/**
	 * Remove an item form the cart
	 * @exception Kohana_User_Exception If the products is not in te cart it launches an exception
	 * @param object $item
	 * @param object $qty [optional]
	 * @return 
	 */
	public function remove_item($item,  $qty = 1){
		$prod_id_col = $this->config['prod_id'];
		if(!isset($this->items[$item->$prod_id_col])){
			throw new Kohana_User_Exception('No item: ', 'This item is not in the cart');
		}
		$this->items[$item->$prod_id_col]['qty'] -= $qty;
		if($this->items[$item->$prod_id_col]['qty'] <= 0 or $qty === 0){
			unset($this->items[$item->$prod_id_col]);
		}
	}
	
	/**
	 * Return all items
	 * @return $items all items in the cart
	 */
	public function get_items(){
		return $this->items;
	}
	
	/**
	 * Returns the total price of the items in the cart
	 * @return $total total price from the cart 
	 */
	public function get_total(){
		$total = 0;
		$price_col = $this->config['price'];
		foreach($this->items as $item){
			$total += $item['qty'] * $item['object']->$price_col; 
		}
		return $total;
	}
	
	/**
	 * Set cart session
	 * @return 
	 */
	public function save_session(){
		$prod_id_col = $this->config['prod_id'];
		$session_array = array();
		foreach($this->items as $item){
			$session_array[$item['object']->$prod_id_col] = $item['qty']; 
		}
		$session = Session::instance();
		$session->set('wcart_items', serialize($session_array));
	}
	
	/**
	 * Delete cart session
	 * @return 
	 */
	public function del_session(){
		$session = Session::instance();
		$session->delete('wcart_items');
	}
	
	/**
	 * Load items from session
	 * @return If isset session returns from the load() method
	 */
	public function load_session(){
		$session = Session::instance();
		$session_array = unserialize($session->get('wcart_items', FALSE));
		if($session_array !== FALSE){
			return $this->load($session_array);
		}
	}
}
