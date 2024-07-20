# MongoDB Task

## Design database for Zen class programme
- users
- codekata
- attendance
- topics
- tasks
- company_drives
- mentors

1. Find all the topics and tasks which are thought in the month of October
    ```javascript
    // Topics
    db.topics.find({
      date: { $gte: ISODate("2020-10-01T00:00:00Z"), $lte: ISODate("2020-10-31T23:59:59Z") }
    });

    // Tasks
    db.tasks.find({
      assigned_date: { $gte: ISODate("2020-10-01T00:00:00Z"), $lte: ISODate("2020-10-31T23:59:59Z") }
    });
    ```
2. Find all the company drives which appeared between 15 oct-2020 and 31-oct-2020
    ```javascript
    db.company_drives.find({
        date: { $gte: ISODate("2020-11-15T00:00:00Z"), $lte: ISODate("2020-11-31T23:59:59Z") }
        });
    ```
3. Find all the company drives and students who are appeared for the placement.
    ```javascript
    db.company_drives.aggregate([
      {
        $lookup: {
          from: "users",
          localField: "participants",
          foreignField: "_id",
          as: "students",
        },
      },
    ]);
    ```
4. Find the number of problems solved by the user in codekata
    ```javascript
    db.codekata.aggregate([
      {
        $match: { status: "solved" },
      },
      {
        $group: {
          _id: "$user_id",
          solved_problems: { $sum: 1 },
        },
      },
    ]);
    ```
5. Find all the mentors with who has the mentee's count more than 15
    ```javascript
    db.mentors.find({
      $expr: { $gt: [{ $size: "$mentees" }, 15] },
    });
    ```
6. Find the number of users who are absent and task is not submitted  between 15 oct-2020 and 31-oct-2020
    ```javascript
    const absentUsers = db.attendance.distinct("user_id", {
      date: { $gte: ISODate("2020-10-15T00:00:00Z"), $lte: ISODate("2020-10-31T23:59:59Z") },
      status: "absent"
    })
    // console.log(absentUsers)
    const taskNotSubmittedUsers = db.tasks.distinct("user_id", {
      due_date: { $gte: ISODate("2020-10-15T00:00:00Z"), $lte: ISODate("2020-10-31T23:59:59Z") },
      status: "pending"
    })
    // console.log(taskNotSubmittedUsers)
    const absentAndNotSubmittedUsers = absentUsers.filter(id => taskNotSubmittedUsers.includes(id));
    // console.log(absentAndNotSubmittedUsers.length);
    const numberOfAbsentAndNotSubmittedUsers = absentAndNotSubmittedUsers.length;
    // console.log(numberOfAbsentAndNotSubmittedUsers)
    ```
