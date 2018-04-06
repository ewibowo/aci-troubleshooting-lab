REST API
========

This is an example of using REST API to interact with APIC.
This script is written in Python. However, you can also use Postman to send the http request.

.. code-block:: console

	import requests
	requests.packages.urllib3.disable_warnings()

	if __name__ == '__main__':
	# variables
	    apic_ip = '192.168.1.1' # OOB mgmt
	    apic_user = 'admin'
	    apic_pw = 'xyz'
	    apic_apic_url = 'https://' + apic_ip + '/api/'

	    # login data
	    login_data = '''<?xml version="1.0" encoding="UTF-8"?>
	                        <imdata totalCount="1">
	                            <aaaUser name="''' + apic_user + '''" pwd="''' + apic_pw + '''"/>
	                    </imdata>'''

	    # create requests session
	    session = requests.session()

	# login to apic (store cookie in requests session)
	    result = session.post(apic_apic_url + 'aaaLogin.xml', data=login_data, verify=False)
    