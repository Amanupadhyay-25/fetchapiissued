# fetchapiissued

const fetchData = async () => {
    const data = await fetch(
     "https://www.swiggy.com/mapi/homepage/getCards?lat=28.6790687&lng=77.4992424"
    );

    const json = await data.json();
    console.log(json);
 
    // Optional Chaining
    setListOfRestaurant(
      json?.data?.success?.cards[1]?.gridWidget?.gridElements?.infoWithStyle?.restaurants
    );
    setfilteredRestaurant( 
      json?.data?.success?.cards[1]?.gridWidget?.gridElements?.infoWithStyle?.restaurants
    );
  };
