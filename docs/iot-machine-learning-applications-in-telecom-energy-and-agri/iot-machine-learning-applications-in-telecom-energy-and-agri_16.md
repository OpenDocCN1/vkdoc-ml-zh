# Split the data into features and target label
cropyield = pd.DataFrame(df['Yield_per_ha'])
dropp=df[['Iron mg/kg','Copper mg/kg','Crop','Center','FarmID','Yield_per_ha']]
features= df.drop(dropp, axis=1)
cropyield.columns
features.columns
#Step 2 Shuffle & Split Final Dataset
