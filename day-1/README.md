## [Day 1: Auderghem](https://sadservers.com/newserver/auderghem) 

**Scenario**: "Auderghem": containers miscommunication

**Level**: Easy

**Description**: There is an nginx Docker container that listens on port 80, the purpose of which is to redirect the traffic to two other containers statichtml1 and statichtml2 but this redirection is not working.
Fix the problem.

IMPORTANT. You can restart all containers, but don't stop or remove them.

**Test**: The nginx container must redirect the traffic to the statichtml1 and statichtml2 containers:

curl http://localhost returns the Welcome to nginx default page
curl http://localhost/1 returns HelloWorld;1
curl http://localhost/2 returns HelloWorld;2 


## Solution

1. Connect the nginx, statichtml1 and statichtml2 to the same network. In this case we use the static-net because statichtmls are already connected and defined in the DNS.
```bash
docker network connect static-net nginx
```

2. Fix the nginx config. Statichtmls uses the :3000, you should redirect to this port to show up the html.
```bash
docker cp nginx:/etc/nginx/conf.d/default.conf ./default.conf  # copying nginx conf

vim default.conf  # add port :3000 to redirects

# use only 1
docker cp ./default.conf nginx:/etc/nginx/conf.d/default.conf  # for not mounted files
cat ./default.conf | docker exec -i nginx sh -c 'cat > /etc/nginx/conf.d/default.conf'  # for mounted files
```

3. Test connection
```bash
curl localhost  # nginx page
curl localhost/1  # statichtml1 page
curl localhost/2  # statichtml2 page
```
