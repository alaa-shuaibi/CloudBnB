# MongoDB Queries

## Query 4
```
db.listings.aggregate([
    {
        $match: {
            $and: [
                {city: "portland"},
                {room_type: "Entire home/apt"}
            ]
        }
    },
    {
        $lookup:{
            from: "calendar",      
            localField: "_id",   
            foreignField: "_id", 
            as: "calendar"
        }
    },
    { $unwind: "$calendar" },
    { $unwind: "$calendar.availability_periods" },
    {    
        $match: {
			$and: [
				{"calendar.availability_periods.available": true},
				{"calendar.availability_periods.start_date": { $gte: new Date("2022-03-01T00:00:00.000Z") }},
				{"calendar.availability_periods.end_date": { $lte: new Date("2022-08-31T00:00:00.000Z") }}
			]
        }
    },
	{
        $group: {
            _id: "$_id",
            "total_available_nights": {$sum: "$calendar.availability_periods.total_nights"}
        }
    }
	
]).pretty();
```
