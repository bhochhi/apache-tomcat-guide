# Apache Tomcat Guide

I want to document this to keep the good understanding of 

- Apache tomcat application server
- Application server as a whole
- Servlet and Servlet Container
- what is Catalina
- How tomcat versioning syncs with java version and other specifications
- et cetera.


How to integrate LDAP with Tomcat 7.
--

We wanted to integrate LDAP with Tomcat to provide authentication mechanism for the web applications hosted on it. Tomcat contains  built-in security features to protect various resources of web applications. These features are called security realms. A realms is not thing but just a database of usernames and passwords that identify valid users of a web application(or set of web applications), plus an enumeration of the list of roles associated with each valid user. Such collections can be mentained  various ways, like storing in relational db, or just in file, or in memory, or served from LDAP directory. Each of these mechanism is implemented using `org.apache.catalina.Realm` interface, creating numerous plug-in components. I am going to explain here which plug-in component to use and how to use LDAP for authentication.

[JDNIRealms](https://tomcat.apache.org/tomcat-7.0-doc/realm-howto.html#JNDIRealm) is the built-in component we need to use for integration LDAP. Follow the steps:

1. Open your CATALINA_HOME/conf/server.xml file, within `<Host>` element, comment out the current `<realm>`(by default `UserDatabaseRealm`) within `LockOutRealm` realm. If you want you can comment out the whole realms.
2. Add the following realm to replace what has just been commented above

    ```xml
    <Realm  	
    	className="org.apache.catalina.realm.JNDIRealm" 
	debug="99"
	connectionURL="ldap://ldap.yourdomain.com" 
	authentication="simple"
	referrals="follow"
	connectionName="ldapquery"
	connectionPassword="password" 
	userSearch="(sAMAccountName={0})"
	userBase="dc=youdomain,dc=com" 
	userSubtree="true" 
	userRoleName="memberOf"
	roleBase="ou=group,dc=yourdomain,dc=com"
	roleSearch="(member={0})"
	roleSubtree="true"
	roleName="CN" />
  ```
  
  **Make sure you know all properties of LDAP protocal their values are set correctly according to your ldap settings.
3. That's it, here on if any of our web application need authentication, just use basic authentication login-config, which should prompt for username/password and if those credentials are entered correctly, your container should verify the user.
4. Remember, this is just authentication parts that your container will handle for you. if you need configuration authorization for your resources, you need to configuration those in your web description file, web.xml.



