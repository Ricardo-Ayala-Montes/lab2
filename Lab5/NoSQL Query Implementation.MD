NoSQL Query Implementation

User Posts Query

´´´
db.posts
  .find({ status: "active" }, { title: 1, likes: 1 })
  .sort({ likes: -1 });
´´´

1. Crear indice para status y ordenamiento likes
    db.posts.createIndex({ status: 1, likes: -1 });
2. Se debe considerar un limit para no obtener tantos reegistros y no se utilicen, para este ejemplo solo 10
    db.posts
    .find({ status: "active" }, { title: 1, likes: 1 })
    .sort({ likes: -1 })
    .limit(10);

3.




User Data Aggregation

´´´
db.users.aggregate([
  { $match: { status: "active" } },
  { $group: { _id: "$location", totalUsers: { $sum: 1 } } },
]);

´´´

1. Crear indice para status y location
    db.users.createIndex({ status: 1, location: 1 });

2. SE debe considerar un limit
    db.users.aggregate([
  { $match: { status: "active" } },
  { $group: { _id: "$location", totalUsers: { $sum: 1 } } },
  { $limit: 10 }
]);
3. Se puede ejecutar Explain() para  obtener informacion de plan de ejecución
db.runCommand(
   {
     explain: {
        update: "db.users.aggregate",
     }
   }
)