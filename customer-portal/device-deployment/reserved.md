---
description: Deploy from a pool of reserved hardware available only to you!
---

# Reserved

Reserved hardware gives you the ability to reserve specific servers for a committed period of time. Unlike hourly on-demand, once you reserve hardware, you will have access to that specific hardware for the duration of the reservation. For additional information, it is suggested to reach out to your specific account manager. If you do not yet have one, please email [support@packet.com](mailto:support@packet.com).

## **Deploy via Portal**

Once you have reserved hardware assigned to your project, you can go to the deploy page and there you will see a section where you can provision your reserved servers as shown here: 

![304053YTKWTZGAHQADCMY0-Screen-Shot-2018-](https://deskpro-cloud.s3.amazonaws.com/files/26944/305/304053YTKWTZGAHQADCMY0-Screen-Shot-2018-12-03-at-8.46.07-PM.png)

When hardware is reserved to your account, the next prompt will allow allow  to choose which hardware device, and OS

## ![304033XZYZXQWXPMHKDCR0-Screen-Shot-2018-](https://deskpro-cloud.s3.amazonaws.com/files/26944/305/304033XZYZXQWXPMHKDCR0-Screen-Shot-2018-12-03-at-8.48.57-PM.png)

## **Moving Reservations between Projects**

Moving hardware reservations between projects within the same organization can be done from the customer portal under  Organization Settings &gt; Reserved Hardware: 

![41829GPNZBDSDDKKHNNH0-1539825594092.png](https://deskpro-cloud.s3.amazonaws.com/files/26944/42/41829GPNZBDSDDKKHNNH0-1539825594092.png)

Click **Manage:** 

![41830XTPDZWYWZCYGNXQ0-1539825594821.png](https://deskpro-cloud.s3.amazonaws.com/files/26944/42/41830XTPDZWYWZCYGNXQ0-1539825594821.png)

Click **Assign to new Project** & choose your specific project name: 

![41831NBWSXNTJSDMAWJK0-1539825595693.png](https://deskpro-cloud.s3.amazonaws.com/files/26944/42/41831NBWSXNTJSDMAWJK0-1539825595693.png)

## **API Endpoints**

There is also  API endpoints to interact with these hardware reservations.

To get a list of all hardware reservations:

```text
curl -X GET -H 'X-Auth-Token: token' 'https://api.packet.net/projects/854fa557-91ed-47f4-a369-1e557998a331/hardware-reservations'
```

By using some `jq`  magic we are able to clean out up the json output to a readable printout: 

```text
{

  "id": "2e0e16b8-8824-44b8-bbcd-9d209adedc5d",

  "provisionable": false

}

{

  "id": "32ffabf2-26e4-4664-9277-2fc035756bb6",

  "provisionable": true

}

{

  "id": "202f186b-b773-499c-bh59-35676d4261a5",

  "provisionable": true

}
```

To create a device using a specific hardware reservation, there is a new JSON parameter hardware\_reservation\_id, e.g.

```text
POST https://api.packet.net/projects/<id>/devices -d '{"hardware_reservation_id":"uuid"}'
```

An example of provisioning reserved hardware in EWR: 

```text
curl -X POST -H "Content-Type: application/json" -H "X-Auth-Token: token" "https://api.packet.net/projects/854fa557-91ed-47f4-a369-1e557998a331/devices" -d '{"hardware_reservation_id":"32ffabf2-2644-4664-9277-2fc035756bb6", "hostname": "ewr-reserved","operating_system": "ubuntu_16_04_image"}'
```

An example of provisioning next available reserved hardware: 

```text
      {
        "hardware_reservation_id": "next-available",
        "plan": "baremetal_1",
        "facility": "abc1",
        "operating_system": "coreos_stable",
        "tags": ["database", "primary"],
        "locked": true
      }
```

