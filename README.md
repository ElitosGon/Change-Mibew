# Mibew Bot Framework Core Change

This repository contains the modified files of the Mibew core, in order to use the Bot Framework plugin, you need to replace these files or modify the originals as you wish.

## Config files

1. config.yml
	Add plugins configs to "plugins" structure in "`<Mibew root>`/configs/config.yml". If the "plugins" stucture looks like `plugins: []` it will become:
	```yaml
	plugins:
	    "TaisaPlus:Bot": # Plugin's configurations are described below
	        ...
	```
2. database_schema.yml
   Add the following lines to the end of the Thread table:
   ```yaml
   # Contains info about chat threads
   thread:
    fields:
        # ID of the thread.
        threadid: "int NOT NULL auto_increment PRIMARY KEY"
        ...
        # ID of the conversatión bot ID, to client rest 
        conversationid: "varchar(255)"
        # Date or ID of send messages to client rest 
        lastbotmessage: "varchar(255)"
    ```
## Libs files

1. libs/classes/Mibew/Thread.php
   Add the following lines as appropriate:
   ```html
   <?php 
	/*
	 * This file is a part of Mibew Messenger.
	 *
	 ...

	class Thread
	{
	    /**
	     * User in the users queue
	     */
	    const STATE_QUEUE = 0;
	    ...
   		<div style="color:green">
	     /**
	     * Content of ID "Conversation-id" bot conversation id.
	     * @var string
	     */
    	public $conversationId;
    
	    /**
	     * Content of ID last message show from bot conversation.
	     * @var string
	     */
    	public $lastBotMessage;
    	
    	</div>
	    /**
	     * Create thread object from database info.
	     ...
	     
    /**
     * Class constructor.
     */
    public function __construct()
    {
        // Set the defaults
        ...
        <div style="color:green">
        $this->conversationId = "0";
        $this->lastBotMessage = "0";
        </div>
    }

    /**
     * Save the thread to the database
     *
     * Triggers {@link \Mibew\EventDispatcher\Events::THREAD_UPDATE} and
     * {@link \Mibew\EventDispatcher\Events::THREAD_CREATE} events.
     *
     * @param boolean $update_revision Indicates if last modified time and last
     *   revision should be updated.
     */
    public function save($update_revision = true)
    {
        ...

        $db = Database::getInstance();
        if (!$this->id) {
            $db->query(
                ('INSERT INTO {thread} ('
                   ...
                   <div style="color:green">
                    . 'shownmessageid, useragent, messagecount, groupid, conversationid, lastbotmessage'
                   </div>
                . ') VALUES ('
                   ... 
                    . ':shown_message_id, :user_agent, :message_count, :group_id , :conversation_id , :last_bot_message'
                . ')'),
                array(
                    ...
                    <div style="color:green">
                    ':conversation_id' => $this->conversationId,
                    ':last_bot_message' => $this->lastBotMessage,
                    </div>
                )
            );
            
            ...

            $db->query(
                ('UPDATE {thread} SET '
                    ...
                    <div style="color:green">
                    . 'conversationid = :conversation_id, '
                    . 'lastbotmessage = :last_bot_message '
                    </div>
                . 'WHERE threadid = :thread_id'),
                array(
                    ...
                    <div style="color:green">
                    ':conversation_id' => $this->conversationId,
                    ':last_bot_message' => $this->lastBotMessage,
                    </div>
                )
            );

            ...
    ...

    /**
     * Sets thread's fields according to the fields from Database.
     *
     * @param array $db_fields Associative array of database fields which keys
     *   are fields names and the values are fields values.
     */
    protected function populateFromDbFields($db_fields)
    {
        ...
        <div style="color:green">
        $this->conversationId = $db_fields['conversationid'];
        $this->lastBotMessage = $db_fields['lastbotmessage'];
        </div>
    }

  	...

    ```
2. libs/classes/Mibew/RequestProcessor/UserProcessor.php
   Add the following lines as appropriate:
   ```html
   <?php
    ...
    ...
    protected function apiUpdateThreads($args)
    {
       ....

       		<div style="color:green">
            // Code for state bot
            $threadState = null;
            $threadMessage = null;
            if($thread->conversationId == '1'){
                $threadState = "Desactivado";
                $threadMessage = "Agente atendiendo";
            }elseif ($thread->conversationId == '-1') {
                $threadState = "BloqueadoA";
                $threadMessage = "Esperando atención:\n Bot no comprende";
            }elseif ($thread->conversationId == '-2'){
                $threadState = "BloqueadoB";
                $threadMessage = "Esperando atención:\n Bot no conoce respuesta";
            }elseif ($thread->conversationId == '-3'){
                $threadState = "BloqueadoC";
                $threadMessage = "Esperando atención:\n Bot no encuentra información";
            }elseif ($thread->conversationId == '-4') {
                $threadState = "BloqueadoD";
                $threadMessage = "Esperando atención:\n Bot no tiene acceso a datos";
            }else{
                $threadState = "Activado";
                $threadMessage = "Bot atendiendo";
            }
            // Code for state bot
             </div> 

            $threads[] = array(
                ...
                <div style="color:green">
                'threadState' => $threadState,
                'threadMessage' => $threadMessage,
                </div>
            );

            ...
        }

        ...
    }

    ...

   ```

## Handlebars files (php views files)
   Review files in repository
1. styles/pages/default/templates_src/client_side/users/threads_collection.handlebars
2. styles/pages/default/templates_src/client_side/users/queued_thread.handlebars
3. styles/pages/default/templates_src/server_side/users.handlebars

   To make changes in Mibew effective they must be rebuilt from the source

## Build from sources

There are several actions one should do before use the latest version of Mibew from the repository:

1. Obtain a copy of the repository using `git clone`, download button, or another way.
2. Install [node.js](http://nodejs.org/) and [npm](https://www.npmjs.org/).
3. Install [Gulp](http://gulpjs.com/).
4. Install npm dependencies using `npm install`.
5. Run Gulp to build the sources using `gulp default`.

Finally `.tar.gz` and `.zip` archives of the ready-to-use Plugin will be available in `release` directory.