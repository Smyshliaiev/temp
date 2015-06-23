# temp

public void insertOrUpdateFlightPlan(FlightPlan fPlan, String email) {
		SQLiteDatabase db = myDataBase;
		Log.d("f18", "insertOrUpdateFlightPlan");
		ContentValues values = new ContentValues();
		values.put(COLUMN_USERID, email);
		values.put(COLUMN_IFPLID, fPlan.getIFPLID());
		values.put(COLUMN_ACID, fPlan.getACID());
		values.put(COLUMN_ADEP, fPlan.getADEP());
		values.put(COLUMN_ADES, fPlan.getADES());
		values.put(COLUMN_EOBT, fPlan.getEOBT());
		values.put(COLUMN_DOF, fPlan.getDOF());
		values.put(COLUMN_EET, fPlan.getEET());
		values.put(COLUMN_STAGE, fPlan.getSTAGE());
		values.put(COLUMN_STATUS, fPlan.getSTATUS());

		values.put(COLUMN_ADEP_ROUTE_NAME, fPlan.getADEP_ROUTE_NAME());
		values.put(COLUMN_ADES_ROUTE_NAME, fPlan.getADES_ROUTE_NAME());
		values.put(COLUMN_ADEP_AIRPORT_NAME, fPlan.getADEP_AIRPORT_NAME());
		values.put(COLUMN_ADES_AIRPORT_NAME, fPlan.getADES_AIRPORT_NAME());
		values.put(COLUMN_FIELD19, fPlan.getFIELD19());
		values.put(COLUMN_FIELD19_SORT, fPlan.getFIELD19_SORT());
		values.put(COLUMN_SEED_TEXT, fPlan.getSEED_TEXT());
		values.put(COLUMN_FLIGHTRULES, fPlan.getFLIGHTRULES());
		values.put(COLUMN_FLIGHTTYPE, fPlan.getFLIGHTTYPE());
		values.put(COLUMN_FIELD18, fPlan.getFIELD18());
		values.put(COLUMN_beforeut, fPlan.getBeforeut());
		values.put(COLUMN_ROUTE, fPlan.getROUTE());
		values.put(COLUMN_FL, fPlan.getFL());
		values.put(COLUMN_FPLID, fPlan.getFPLID());
        values.put(COLUMN_FLTNUM, fPlan.getFLTNUM());
        values.put(COLUMN_AIRCRAFT_ID, fPlan.getAIRCRAFT_ID());
		values.put("windft", "");

		Cursor curs = db.rawQuery("select " + COLUMN_IFPLID + " from " + TABLE_FLIGHTS + " where " + COLUMN_IFPLID + "='" + fPlan.getIFPLID() + "' and " + COLUMN_USERID + "='"
				+ email + "'", null);
		if (curs != null && curs.getCount() > 0) {
			// update
			int response = db.update(TABLE_FLIGHTS, values, COLUMN_IFPLID + " = ? and " + COLUMN_USERID + " = ? ", new String[] { fPlan.getIFPLID(), email });
			Log.d("f18", "updated: " + String.valueOf(response));
		} else {
			// insert
			long response = db.insert(TABLE_FLIGHTS, null, values);
			Log.d("f18", "inserted: " + String.valueOf(response));
		}
	}

	public void clearFlightPlans(ArrayList<FlightPlan> receivedPlans, String email) {
		SQLiteDatabase db = myDataBase;
		Log.d("f18", "removeOldFlightPlans start");
		if (receivedPlans != null) {
			Cursor curs = null;
			try {
				curs = db.rawQuery("select * from " + TABLE_FLIGHTS + " where " + COLUMN_USERID + "='" + email + "'", null);
				if (curs != null && curs.getCount() > 0) {
					curs.moveToFirst();
					do {

						String IFPLID = curs.getString(curs.getColumnIndex(COLUMN_IFPLID));

						boolean toDelete = true;
						for (int i = 0; i < receivedPlans.size(); i++) {
							if (IFPLID.compareTo(receivedPlans.get(i).getIFPLID()) == 0) {
								toDelete = false;
								// break;
							}
						}

						if (toDelete) {
							String queryString = "delete from " + TABLE_FLIGHTS + " where " + COLUMN_IFPLID + "='" + IFPLID + "'";
                            myDataBase.execSQL(queryString);

                            // delete BP

                            clearBPDocs(IFPLID);
						}

					} while (curs.moveToNext());

				}
				if (curs != null)
					curs.close();
			} catch (NullPointerException e) {

			} finally {
				if (curs != null)
					curs.close();
			}
		}
		Log.d("f18", "removeOldFlightPlans finish");
	}
