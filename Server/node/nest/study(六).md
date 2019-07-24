# nestjs学习（六）

## 扩展学习 mongoose相关操作

1. 相关操作
   * Model.deleteMany()
   * Model.deleteOne()
   * Model.find()
   * Model.findById()
   * Model.findByIdAndDelete()
   * Model.findByIdAndRemove()
   * Model.findByIdAndUpdate()
   * Model.findOne()
   * Model.findOneAndDelete()
   * Model.findOneAndRemove()
   * Model.findOneAndUpdate()
   * Model.replaceOne()
   * Model.updateMany()
   * Model.updateOne()

2. query

        // With a JSON doc
        Person.
          find({
            occupation: /host/,
            'name.last': 'Ghost',
            age: { $gt: 17, $lt: 66 },
            likes: { $in: ['vaporizing', 'talking'] }
          }).
          limit(10).
          sort({ occupation: -1 }).
          select({ name: 1, occupation: 1 }).
          exec(callback);

        // Using query builder
        Person.
          find({ occupation: /host/ }).
          where('name.last').equals('Ghost').
          where('age').gt(17).lt(66).
          where('likes').in(['vaporizing', 'talking']).
          limit(10).
          sort('-occupation').
          select('name occupation').
          exec(callback);

        // Using populate  获取外码相关表
        User.
        findOne({ name: 'Val' }).
        populate({
          path: 'friends',
          // Get friends of friends - populate the 'friends' array for every friend
          populate: { path: 'friends' } // 获取外码的外码
        });

