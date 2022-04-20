```c#
﻿using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Data;
using System.Data.OleDb;

namespace Equip
{
    class DBHelper
    {
        
        //private static string connStr = "Provider=Microsoft.ace.OLEDB.12.0;Data Source=" + System.AppDomain.CurrentDomain.BaseDirectory + "\\Equip.accdb";
        private static string connStr = @"Provider=Microsoft.ace.OLEDB.12.0;Data Source=D:\EquipmentData\Equip.accdb";
       
        #region 返回连接字符串
        public static string getConnectionString()
        {
            return connStr;
        }
        #endregion

        #region ExecuteNonQuery 返回受影响的行数（增、删、改）
        /// <summary>
        /// 
        /// </summary>
        /// <param name="sql"></param>
        /// <param name="cmdType"></param>
        /// <param name="prams"></param>
        /// <returns></returns>
        public static int ExecuteNonQuery(string sql, CommandType cmdType = CommandType.Text, params OleDbParameter[] prams)
        {
            using (OleDbConnection conn = new OleDbConnection(connStr))
            {
                using (OleDbCommand cmd = new OleDbCommand(sql, conn))
                {
                    cmd.CommandType = cmdType;
                    if (prams != null)
                    {
                        cmd.Parameters.AddRange(prams);
                    }
                    try
                    {
                        if (conn.State == ConnectionState.Closed)
                        {
                            conn.Open();
                        }
                        return cmd.ExecuteNonQuery();
                    }
                    catch
                    {
                        throw;
                    }

                }
            }
        }
        #endregion

        #region ExcuteScalar  返回首行首列
        public static object ExecuteScalar(string sql, CommandType cmdType = CommandType.Text, params OleDbParameter[] prams)
        {
            using (OleDbConnection conn = new OleDbConnection(connStr))
            {
                using (OleDbCommand cmd = new OleDbCommand(sql, conn))
                {
                    cmd.CommandType = cmdType;
                    if (prams != null)
                    {
                        cmd.Parameters.AddRange(prams);
                    }

                    try
                    {
                        if (conn.State == ConnectionState.Closed)
                        {
                            conn.Open();
                        }
                        return cmd.ExecuteScalar();
                    }
                    catch
                    {
                        throw;
                    }

                }
            }
        }
        #endregion

        #region 返回ExecuteReader
        public static OleDbDataReader ExecuteReader(string sql, CommandType cmdType = CommandType.Text, params OleDbParameter[] prams)
        {
            OleDbConnection conn = new OleDbConnection(connStr);

            using (OleDbCommand cmd = new OleDbCommand(sql, conn))
            {
                cmd.CommandType = cmdType;
                if (prams != null)
                {
                    cmd.Parameters.AddRange(prams);
                }
                
                try
                {
                    if (conn.State == ConnectionState.Closed)
                    {
                        conn.Open();
                    }
                    return cmd.ExecuteReader(CommandBehavior.CloseConnection);
                }
                catch
                {
                    conn.Close();
                    conn.Dispose();
                    //throw;
                }

            }

        }
        #endregion

        #region 返回DataTable
        public static DataTable ExecuteDataTable(string sql, CommandType cmdType = CommandType.Text, params OleDbParameter[] prams)
        {
            DataTable dt = new DataTable();
            using (OleDbDataAdapter adapter = new OleDbDataAdapter(sql, connStr))
            {
                adapter.SelectCommand.CommandType = cmdType;
                if (prams != null)
                {
                    adapter.SelectCommand.Parameters.AddRange(prams);
                }
                adapter.Fill(dt);
            }
            return dt;
        }
        #endregion

        #region 返回DataAdapter
        public static OleDbDataAdapter ExecuteDataAdapter(string sql, CommandType cmdType = CommandType.Text, params OleDbParameter[] prams)
        {
            OleDbDataAdapter adapter = new OleDbDataAdapter(sql, connStr);
            adapter.SelectCommand.CommandType = cmdType;
            if (prams != null)
            {
                adapter.SelectCommand.Parameters.AddRange(prams);
            }          

            return adapter;
        }
        #endregion
    }

}

```

