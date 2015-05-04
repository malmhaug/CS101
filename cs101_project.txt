# ----------------------------------------------------------------------------- 
# create_data_structure(string_input): 
#   Parses a block of text (such as the one above) and stores relevant 
#   information into a data structure.
# 
# Arguments: 
#   string_input: block of text containing the network information.
#
# Variables/Parameters:
#   network: the gamer network data structure.
#   user_list: contains a text string with informations about each users connections and games liked.
#   entry: used for splitting and fitting the right information into the network.
# 
# Functions:
#   split(): a function which splits a text string by arguments.
#
# Return:
#   The network data structure. A dictonary with dictonaries => 
#   {user_1: {connections: [], games: []},...,user_n: {connections: [], games: []}}

def create_data_structure(string_input):
    network = {}
    if string_input == '':
        return network
    user_list = string_input.split(".")
    for entry in user_list:
        if " is connected to " in entry:
            entry = entry.split(" is connected to ")
            entry[1] = entry[1].split(", ")
            network[entry[0]] = {'connections': entry[1]}
        if " likes to play " in entry:
            entry = entry.split(" likes to play ")
            entry[1] = entry[1].split(", ")
            network[entry[0]]['games'] = entry[1]
    return network

# ----------------------------------------------------------------------------- 
# get_connections(network, user): 
#   Returns a list of all the connections that user has.
#
# Arguments: 
#   network: the gamer network data structure.
#   user:    a string containing the name of the user.
# 
# Return: 
#   A list of all connections the user has.
#   - If the user has no connections, empty list is returned.
#   - If the user is not in network, then None is returned.

def get_connections(network, user):
    if user not in network:
        return None
    if network[user]['connections'] == []:
        return []
    return network[user]['connections']

# ----------------------------------------------------------------------------- 
# get_games_liked(network, user): 
#   Returns a list of all the games a user likes.
#
# Arguments: 
#   network: the gamer network data structure.
#   user:    a string containing the name of the user.
# 
# Return: 
#   A list of all games the user likes.
#   - If the user likes no games, empty list is returned.
#   - If the user is not in network, None is returned.

def get_games_liked(network,user):
    if user not in network:
        return None
    if network[user]['games'] == []:
        return []
    return network[user]['games']

# ----------------------------------------------------------------------------- 
# add_connection(network, user_A, user_B): 
#   Adds a connection from user_A to user_B.
# 
# Arguments: 
#   network: the gamer network data structure. 
#   user_A:  a string with the name of the user the connection is from.
#   user_B:  a string with the name of the user the connection is to.
#
# Return: 
#   The updated network with the new connection added.
#   - If a connection already exists from user_A to user_B, network is returned unchanged.
#   - If user_A or user_B is not in network, False is returned.

def add_connection(network, user_A, user_B):
    if user_A not in network or user_B not in network:
        return False
    if user_B not in get_connections(network, user_A):
        network[user_A]['connections'] += [user_B]
    return network

# ----------------------------------------------------------------------------- 
# add_new_user(network, user, games): 
#   Creates a new user profile and adds that user to the network, along with
#   any game preferences specified in games. Assumes that the user has no 
#   connections to begin with.
# 
# Arguments:
#   network: the gamer network data structure.
#   user:    a string containing the name of the user to be added to the network.
#   games:   a list of strings containing the user's favorite games, e.g.:
#		     ['Ninja Hamsters', 'Super Mushroom Man', 'Dinosaur Diner'].
#
# Return: 
#   The updated network with the new user and game preferences added. The new user 
#   has no connections.
#   - If the user already exists in network, network is returned unchanged.

def add_new_user(network, user, games):
    if user not in network:
        network[user] = {'connections': [], 'games': games}
    return network
		
# ----------------------------------------------------------------------------- 
# get_secondary_connections(network, user): 
#   Finds all the secondary connections (i.e. connections of connections) of a 
#   given user.
# 
# Arguments: 
#   network: the gamer network data structure.
#   user:    a string containing the name of the user.
#
# Variables/Parameters:
#   secondary_list: a list of a users secondary connections.
#   primary_connections: a users connections.
#   primary_person: used for looping through the primary_connections of a given user.
#   secondary_person: used for looping through the list secondary_list.
#
# Functions:
#   get_connections(): gets user connections.
#
# Return: 
#   A list containing the secondary connections (connections of connections).
#   - If the user is not in the network, None is returned.
#   - If a user has no primary connections to begin with, empty list is returned.

def get_secondary_connections(network, user):
    if user not in network:
        return None
    secondary_list = []
    primary_connections = get_connections(network, user)
    for primary_person in primary_connections:
        secondary_connections = get_connections(network,primary_person)
        for secondary_person in secondary_connections:
            if secondary_person not in secondary_list:
                secondary_list += [secondary_person]
    return secondary_list

# ----------------------------------------------------------------------------- 	
# connections_in_common(network, user_A, user_B): 
#   Finds the number of people that user_A and user_B have in common.
#  
# Arguments: 
#   network: the gamer network data structure.
#   user_A:  a string containing the name of user_A.
#   user_B:  a string containing the name of user_B.
#
# Variables/Parameters:
#   common_persons: an integer with information on how many common persons user_A and user_B has.
#   user_A_connections: a list of user_A connections.
#   user_B_connections: a list of user_B connections.
#
# Functions:
#   get_connections(): gets user connoections.
#
# Return: 
#   The number of connections in common (as an integer).
#   - If user_A and user_B is not in network, false is returned.

def connections_in_common(network, user_A, user_B):
    common_persons = 0
    if user_A in network and user_B in network:
        user_A_connections = get_connections(network, user_A)
        user_B_connections = get_connections(network, user_B)
        for person in user_B_connections:
            if person in user_A_connections:
                common_persons += 1
    else:
        return False
    return common_persons

# ----------------------------------------------------------------------------- 
# path_to_friend(network, user_A, user_B): 
#   Finds a connections path from user_A to user_B. Does not necessarily return the shotest path.
#   Uses the helper function path_helper().
#   
# Arguments:
#   network: The network you created with create_data_structure. 
#   user_A:  String holding the starting username.
#   user_B:  String holding the ending username.
#
# Variables/Parameters:
#   path: a list which shows the path from user_A to user_B. Is also an argument in the helper function.
#   user_A_connections: a list of user_A connections.
#   tentative_return: used for getting a recursed return from path_helper.
#   friend: used for looping through user_A_connections. Is also used as an argument in the recursed
#           helper function.
#
# Functions:
#   get_connections(): gets user connections.
# 
# Return:
#   A list showing the path from user_A to user_B.
#   - If such a path does not exist, None is returned.
#   - If user_A or user_B is not in network, None is returned.
#   - If user_A is user_B, user_A is returned.

def path_to_friend(network, user_A, user_B):
    if user_A not in network or user_B not in network:
        return None
    if user_A is user_B:
        return user_A
    return path_helper(network, user_A, user_B, [])

def path_helper(network, user_A, user_B, path):
    path = path + [user_A]
    user_A_connections = get_connections(network, user_A)
    if user_B in user_A_connections:
        return path + [user_B]
    for friend in user_A_connections:
        if friend not in path:
            tentative_return = path_helper(network, friend, user_B, path)
            if tentative_return is not None:
                return tentative_return
    return None
            
    
# -----------------------------------------------------------------------------
# MYOP:
# find_game_players(network, user):
#   Returns a list of all the games the user plays, with number of other players who play the same game.
#   -> [[game_1, # game_1 players in network],[game_2, # game_2 players in network],...,
#   [game_n, # game_n players in network]]
#
# Arguments: 
#   network: the gamer network data structure
#   user:    a string containing the name of the user
#
# Variables/Parameters:
#   user_connections: a list of user connections.
#   games_palying: a list of which games a user plays.
#   common_players: a list which contains information about games and number of specific players in the
#                   network.
#   game: used for looping through the list games_playing.
#   nr_gamers: integer which counts how many players who plays a game in the network.
#
# Functions:
#   get_connections(): gets user connections.
#   get_games_liked(): gets user games.
# 
# Return: 
#   A list of all the games the user plays and how many other common gamers there are in the network.
#   - If the user has no connections, empty list is returned.
#   - If the user is not in network, None is returned.


def find_game_players(network, user):
    if user not in network:
        return None
    user_connections = get_connections(network, user)
    if user_connections == []:
        return []
    games_playing = get_games_liked(network,user)
    common_players = []
    for game in games_playing:
        nr_gamers = 0
        for user in network:
            if game in get_games_liked(network,user):
                nr_gamers += 1
        common_players += [[game,nr_gamers]]
    return common_players