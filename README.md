# fetchapiissued

#This is mycode which only run in inspect mode 
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



  #Inspects mode issue solved (Body.js)

  import RestaurantCard ,{withPromotedLabel}from "./RestaurantCard";
import { useContext, useEffect, useState ,useContext} from "react";
import Shimmer from "./Shimmer";
import { Link } from "react-router-dom";
import useOnlineStatus from "../utils/useonlineStatus";
import UserContext from "../utils/UserContext";



const Body=(props)=>{

  const [listOfRestaurants,setListOfRestaurant]=useState([]);
  const [filteredRestaurant,setfilteredRestaurant]=useState([]);

  // console.log(listOfRestaurants);


  const [searchText,setsearchText]=useState("");

  const RestaurantCardPromoted=withPromotedLabel(RestaurantCard);

  const {loggedInUser,setUserName}=useContext(UserContext);


  useEffect(()=>{
   fetchData();
  },[]);


  const fetchData = async () => {
    const data = await fetch("https://www.swiggy.com/dapi/restaurants/list/v5?lat=21.1702401&lng=72.83106070000001&page_type=DESKTOP_WEB_LISTING")
		const json = await data.json()
		console.log(json)
		console.log("json", json?.data?.cards[1]?.card?.card?.gridElements?.infoWithStyle?.restaurants)
		setListOfRestaurant(json?.data?.cards[1]?.card?.card?.gridElements?.infoWithStyle?.restaurants)
		console.log("list", listOfRestaurants)
		try {
			const data = await fetch("https://www.swiggy.com/dapi/restaurants/list/v5?lat=21.1702401&lng=72.83106070000001&page_type=DESKTOP_WEB_LISTING")
			const json = await data.json()
			console.log("json", json)

			let resList = json?.data?.cards[1]?.card?.card?.gridElements?.infoWithStyle?.restaurants || json?.data?.cards[4]?.card?.card?.gridElements?.infoWithStyle?.restaurants
			console.log("json", resList)
			setfilteredRestaurant(resList || []); 
			console.log("listOfRestaurants", listOfRestaurants)
		} catch (error) {
			console.log(error)
		}
  };

  const onlineStatus=useOnlineStatus();
    
  if(onlineStatus===false)
     return(
  <h1>
    Heyy gunnnu !! Apna net toh on karlo !! 😍
    </h1>
    );

   return filteredRestaurant.length === 0?<Shimmer/>:(
        <div className="body">
        <div className="filter flex">
        <div className="search m-4 p-4" >
        <input 
        className="border border-solid border-black "
          type="text"
          value={searchText}
          onChange={(e)=>{
              setsearchText(e.target.value);
          }}
        />
        <button
        className="px-4 py-1 bg-green-200 m-4 rounded-lg"
         onClick={()=>{
          const filteredRestaurant=listOfRestaurants.filter((res)=>{
            return  res.info.name.toLowerCase().includes(searchText.toLowerCase())
          })
          setfilteredRestaurant(filteredRestaurant);
         }}
        >Search</button> 
        </div> 
        <div className="search m-4 p-4 flex items-center ">
         < button className="px-4 py-2 bg-gray-400 rounded-lg"
         onClick={()=>{
          const filteredlist =listOfRestaurants.filter((res)=>
            res.info.avgRating>3.8
          )
          setListOfRestaurant(filteredlist);
         }}>
          Top-Rated Restaurant
          </button>
          </div>
          <div className="search m-4 px-2 flex items-center">
            <label>UserName:</label>
            <input className="border border-black" 
            value={loggedInUser}
            onChange={(e)=>{
              setUserName(e.target.value)
            }}
             />
         </div>
         </div>
        <div className="flex flex-wrap">
        {filteredRestaurant.map((restaurant)=>(
            <Link 
            key={restaurant.info.id}
            to={"/restaurants/"+restaurant.info.id}>
             { 
             //check id prototed true then add it in and enhance RestaurantCard otherwise print card without promoted label 
              restaurant.info.promoted? <RestaurantCardPromoted  resData={restaurant} 
              />:<RestaurantCard  resData={restaurant}/>
             } 
            </Link>
          ))}
        </div>
        </div>
    );
};

export default Body
  

  
