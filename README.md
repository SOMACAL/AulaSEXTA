# AulaSEXTA
from flask import Flask, jsonify,request
import mysql.connector

app = Flask(__name__)

NOT_FOUND = 404
SUCCESS = 200
SERVER_ERROR = 500

config = {
    "user": "root",
    "password": "positivo",
    "host": "localhost",
    "database": "users"
}

@app.route("/")
def hello_word():
    return"<p>hello_word</p>"

@app.route("/user", methods=["PUT"])
def update_user():
    id = request.args.get("id")

    if id is None:
        return standard_message_return("Insert id of valid user", NOT_FOUND)

    connection = mysql.connector.connect(**config)

    if not connection.is_connected():
        return standard_message_return("Problem connecting to the database", SERVER_ERROR)
    
    cursor = connection.cursor()
 
    cursor.execute("SELECT * FROM user WHERE id = " + str(id))

    user = cursor.fetchone()
    if user is None:
            return standard_message_return("This user does not exist on database, please inform a user that exist",
             NOT_FOUND
             )

    body =  request.get_json()
    print(body)

    return standard_message_return("The update was done with success", SUCCESS)


def standard_message_return(message, httpCode):
    return jsonify({"message": message}), httpCode

if __name__ == "__main__":
    app.run(port=3000, debug=True)
