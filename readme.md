# A simple PHP CAPTCHA script for CodeIgniter

This is a fork of Cory LaViska's simple, HIGHLY readable PHP captcha: https://github.com/claviska/simple-php-captcha

This is the exact same script, modified to work with the CodeIgniter PHP framework.

## Original Demo

http://labs.abeautifulsite.net/simple-php-captcha/


## Requirements

* Requires PHP GD library
* Background images must be in PNG format
* Fonts can be either TTF or OTF format

This code was tested with CodeIgniter 2.1.3.

## Installation

- Move the controller file to your controllers directory.
- Move the libraries file to your libraries directory.
- Move the entire captcha directory to wherever you keep your assets (images, javascript, css, etc)
- You will need to change the `png_backgrounds` and `fonts` settings in the library file to wherever you keep your assets, or simply pass an array with the new configuration when calling `$this->captcha->main()`.

## Usage

Place this code in the controller that processes the form:
	
	$this->load->library('captcha');
	$data['captcha'] = $this->captcha->main();
	$this->session->set_userdata('captcha_info', $data['captcha']);

You can then pass `$data['captcha']` to your view and show the captcha image like so:

	<img src="<?php echo $captcha['image_src'];?>" alt="CAPTCHA security code" />

To validate the captcha code after the form is submitted, compare the user's input to the `captcha_info` userdata, in the `code` value. E.g.:

	$captcha_info = $this->session->userdata('captcha_info');
	if ($captcha_info['code'] != $this->input->post('captcha'))
	{
		//don't process the form
	}

Or, you can use the validation function below.

## Validation Function

You can use this function in conjunction with the form_validation class. Place this function in the same controller.

	/**
	 * verifies that the captcha is correct
	 * @param  string $str the captcha string
	 * @return bool        true on pass, false on failure
	 */
	public function captcha($str)
	{
		$captcha_info = $this->session->userdata('captcha_info');
		if ($captcha_info['code'] != $str)
		{
			$this->form_validation->set_message('captcha', 'The %s was not input correctly. Please try again.');
			return false;
		}

		return true;
	}

Add this rule to your form to use the function:

	$this->form_validation->set_rules('captcha', 'CAPTCHA', 'trim|required|callback_captcha|xss_clean');